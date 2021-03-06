CONVERSION ERROR

Original source:

```trac
= Improving LLVM Alias Analysis =

This page tracks the information and progress relevant to improving the alias analysis pass for the LLVM backend of GHC.

This correspond to bug #5567.

== LLVM Alias Analysis Infrastructure ==

Some links to the various documentation on LLVM's AA support:

 * [http://llvm.org/docs/AliasAnalysis.html LLVM Alias Analysis Infrastructure]
 * [http://llvm.org/docs/Passes.html LLVM's Analysis and Transform Passes]
 * [http://llvm.org/docs/GetElementPtr.html The Often Misunderstood GEP Instruction]
 * [http://llvm.org/docs/LangRef.html LLVM Language Reference]
 * [http://groups.google.com/group/llvm-dev/browse_thread/thread/2a5944692508bcc2/363c96bb1c6a506d?show_docid=363c96bb1c6a506d&pli=1 LLVM Dev List: Comparison of Alias Analysis in LLVM]

== Max's Work ==

Max had a crack at writing a custom alias analysis pass for LLVM, relevant links are:

 * [http://lists.cs.uiuc.edu/pipermail/llvmdev/2011-September/043603.html Email to LLVM dev]
 * [http://blog.omega-prime.co.uk/?p=135 Blog post about results]
 * [https://github.com/bgamari/ghc-llvm-analyses A port to LLVM 3.6]
== TBAA ==

LLVM as of version 2.9 includes Type Based Alias Analysis. This mean using metadata you can specify a type hierarchy (with alias properties between types) and annotate your code with these types to improve the alias information. This should allow us to improve the alias analysis without any changes to LLVM itself like Max made.

 * [http://llvm.org/docs/LangRef.html#tbaa LLVM TBBA Doc]

== STG / Cmm Alias Properties ==

'''Question''' (David Terei): What alias properties does the codegen obey? Sp and Hp never alias? R<n> registers never alias? ....

'''Answer''' (Simon Marlow): Sp[] and Hp[] never alias, R[] never aliases with Sp[], and that's about it.

{{{
I64[ Sp + n ] = "stack"

I64[ Base + n ] = "base"

I64[ Hp + n ] = "heap"
I64[ R1 + n ] = "heap"

I64[ I64[Sp + n]  ] = "heap"
I64[ I64[Sp + n] + m  ] = "heap"

I64[ I64[R1 + n] ] = "heap"
I64[ I64[R1 + n] + m ] = "heap"
I64[ I64[Sp + n] +  I64 [R1 + n] ] = "heap"
}}}

''' Simon''': As long as it propagates properly, such that every F(Sp) is a stack pointer, where F() is any expression context except a dereference.  That is, we better be sure that

{{{
I64[Sp + R1[n]]
}}}

is "stack", not "heap".

== How to Track TBAA information ==

Really to be sound and support Cmm in full we would need to track and propagate TBAA information. It's Types after all! At the moment we don't. We simply rely on the fact that the Cmm code generated for loads and stores is nearly always in the form of:

{{{
I64[ Sp ... ] = ...
}}}

That is to say, it has the values it depends on for the pointer derivation in-lined in the load or store expression. It is very rarely of the form:

{{{
x = Sp + 8
I64[x] = ...
}}}

And when it is, 'it is' (unconfirmed) always deriving a "heap" pointer, "stack" pointers are always of the in-line variety. This assumption if true allows us to look at just a store or load in isolation to properly Type it.

There are two ways to type this 'properly'.

1. Do data flow analysis. This is the only proper way to do it but also annoying.
2. Do block local analysis. Instead of doing full blow data flow analysis, just track the type of pointers stored to CmmLocal regs at the block level. This is safe but just may miss some opportunities when a CmmLocal's value is assigned in another block... My hunch is this is quite rare so this method should be fairly effective (and easier to implement and quicker to run that 1.)

== LLVM type system ==

The above aliasing information can be encoded as follows:

{{{
!0 = metadata !{ metadata !"top" }
!1 = metadata !{ metadata !"heap", metadata !0 }
!2 = metadata !{ metadata !"stack", metadata !0 }
!3 = metadata !{ metadata !"rx", metadata !1 }
!4 = metadata !{ metadata !"base", metadata !0 }
!5 = metadata !{ metadata !"other", metadata !0 }
}}}

The fact that `R[]` never aliases with `Sp[]` is never used as the one way relation isn't expressible in LLVM.

Stores/loads needs to be annotated with `!tbaa` and one of the above four types e.g.

{{{
%ln1NH1 = load i64* %Sp_Arg, align 8, !tbaa !2
}}}

== Problems / Optmisations to Solve ==

=== LLVM Optimisations ===

Roman reported that running 'opt -std-compile-opts' gives much better code than running 'opt -O3'.

'''Following is from Roman Leschinskiy'''

'-O2 -std-compile-opts' does the trick but it's obviously overkill because
it essentially executes the whole optimisation pipeline twice. The crucial
passes seem to be loop rotation and loop invariant code motion. These are
already executed twice by -O2 but it seems that they don't have enough
information then and that something interesting happens in later passes
which allows them to work much better the third time.

=== Safe Loads (speculative load) ===

We want to allow LLVM to speculatively hoist loads out of conditional blocks. Relevant LLVM source code is here:

 * [http://llvm.org/docs/doxygen/html/SimplifyCFG_8cpp_source.html SimplifyCFG Source Code]
 * [http://llvm.org/docs/doxygen/html/namespacellvm.html#a4899ff634bf732c16dd22ecfdafdea7d llvm::isSafeToSpeculativelyExecute]
 * [http://lists.cs.uiuc.edu/pipermail/llvmdev/2012-January/046958.html LLVM Mailing List Discussion about 'Safe loads']

'''Following is from Roman Leshchinskiy'''

I've poked around a bit and things are rather complicated. So far I've identified two problems. Here is a small example function:

{{{
 foo as n = loop 0# 0.0##
  where
    loop i x
      | i >=# n = (# x, I# i #)
      | otherwise = loop (i +# 1#) (x *## indexDoubleArray# as i)
}}}

This is the interesting C-- bit:

{{{
 saH_ret()
    cb0:
        Hp = Hp + 8;
        if (Hp > I32[BaseReg + 92]) goto cb5;
        ;
        if (%MO_S_Ge_W32(R1, I32[Sp + 16])) goto cb9;
        _saO::I32 = R1 + 1;
        F64[Sp + 0] = %MO_F_Mul_W64(F64[Sp + 0],
                                    F64[I32[Sp + 12] + ((R1 << 3) + 8)]);
        R1 = _saO::I32;
        Hp = Hp - 8;
        jump saH_info; // [R1]
}}}

Look at what indexDoubleArray# compiles to: F64[I32[Sp + 12] + ((R1 << 3) + 8)]. We would very much like LLVM to hoist the I32[Sp+12] bit (i.e., loading the pointer to the ByteArray data) out of the loop because that might allow all sorts of wonderful optimisation such as promoting it to a register. But alas, this doesn't happen, LLVM leaves the load in the loop. Why? Because it assumes that the load might fail (for instance, if Sp is NULL) and so can't move it past conditionals. We know, of course, that this particular load can't fail and so can be executed speculatively but there doesn't seem to be a way of communicating this to LLVM.

As a quick experiment, I hacked LLVM to accept "safe" annotations on loads and then manually annotated the LLVM assembly generated by GHC and that helped quite a bit. I suppose that's the way to go - we'll have to get this into LLVM in some form and then the backend will have to generate those annotations for loads which can't fail. I assume they are loads through the stack pointer and perhaps the heap pointer unless we're loading newly allocated memory (those loads can't be moved past heap checks). In any case, the stack pointer is the most important thing. I can also imagine annotating pointers (such as Sp) rather than instructions but that doesn't seem to be the LLVM way and it's also less flexible.

=== GHC Heap Check (case merging) ===

See bug #1498

'''Following is from Roman Leshchinskiy'''

I investigated heap check a bit more and it seems to me that it's largely
GHC's fault. LLVM does do loop unswitching which correctly pulls out
loop-invariant heap checks but that happens fairly late in its pipeline
and heap checks interfere with optimisations before that.

However, we really shouldn't be generating those heap checks in the first
place. Here is a small example loop:

{{{
 foo as n = loop 0# 0.0##
  where
    loop i x
      | i >=# n = (# (), D# x #)
      | otherwise = loop (i +# 1#) (x *## indexDoubleArray# as i)
}}}

This is the C-- that GHC generates:

{{{
 sep_ret()
    ceO:
        Hp = Hp + 12;
        if (Hp > I32[BaseReg + 92]) goto ceT;

        if (%MO_S_Ge_W32(R1, I32[Sp + 16])) goto ceX;

        _seA::I32 = R1 + 1;
        F64[Sp + 0] = %MO_F_Mul_W64(F64[Sp + 0], F64[I32[Sp + 12] + ((R1 << 3) + 8)]);
        R1 = _seA::I32;
        Hp = Hp - 12;
        jump sep_info ();
    ceT:
        I32[BaseReg + 112] = 12;
        goto ceR;
    ceR:
        I32[Sp + 8] = sep_info;
        I32[BaseReg + 32] = 131327;
        jump stg_gc_ut ();
    ceX:
        I32[Hp - 8] = GHC.Types.D#_con_info;
        F64[Hp - 4] = F64[Sp + 0];
        I32[Sp + 16] = Hp - 7;
        R1 = GHC.Unit.()_closure+1;
        Sp = Sp + 16;
        jump (I32[Sp + 4]) ();
}}}

Note how in each loop iteration, we add 12 to Hp, then do the heap check
and  then subtract 12 from Hp again. I really don't think we should be
generating that and then relying on LLVM to optimise it away.

This happens because GHC commons up heap checks for case alternatives and
does just one check before evaluating the case. The relevant comment from
CgCase.lhs is this:

A more interesting situation is this:

{{{
          !A!;
          ...A...
          case x# of
            0#      -> !B!; ...B...
            default -> !C!; ...C...
}}}

where !x! indicates a possible heap-check point. The heap checks
in the alternatives '''can''' be omitted, in which case the topmost
heapcheck will take their worst case into account.

This certainly makes sense if A allocates. But with vector-based code at
least, a lot of the time neither A nor C will allocate '''and''' C will
tail-call A again so by pushing the heap check into !A!, we are now doing
it '''in''' the loop rather than at the end.

It seems to me that we should only do this if A actually allocates and
leave the heap checks in the alternatives if it doesn't (perhaps we could
also use a common heap check if '''all''' alternatives allocate). I tried to
hack this and see what happens but found the code in CgCase and friends
largely incomprehensible. What would I have to change to implement this
(perhaps controlled by a command line flag) and is it a good idea at all?

```
