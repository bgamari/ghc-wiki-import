CONVERSION ERROR

Original source:

```trac
== Rationale ==

`unsafeCoerce#` allow unsafe coercions between different types.
According to the [http://hackage.haskell.org/package/ghc-prim-0.3.1.0/docs/GHC-Prim.html#v:unsafeCoerce-35- documentation in GHC.Prim], it is safe only for following uses:

  * Casting any lifted type to `Any`

  * Casting `Any` back to the real type

  * Casting an unboxed type to another unboxed type of the same size (but not coercions between floating-point and integral types)

  * Casting between two types that have the same runtime representation. One case is when the two types differ only in "phantom" type parameters, for example `Ptr Int` to `Ptr Float`, or `[Int]` to `[Float]` when the list is known to be empty. 

  * Casting between a newtype of a type `T` and `T` itself.  ('''RAE:''' This last usecase is subsumed by `Data.Coerce.coerce`, at least when the newtype constructor is in scope.)

However GHC doesn't check if it's safe to use `unsafeCoerce#` as a result bugs can appear, see [ticket:9035].
In order to solve this problem a solution was proposed by Simon in [ticket:9122], quote:

> I think it would be a great idea for Core Lint to check for uses of `unsafeCoerce` that don't obey the rules. It won't catch all cases, of course, but it would have caught #9035. 

This proposal is about implementation of the task.

== Progress ==

Current progress could be found on [Phab:D637](https://phabricator.haskell.org/D637). It implements
proposed checks modulo few questions mentioned in this proposal. 

The solution introduces following
changes in the core specification, `docs/core-spec/CoreLint.ott` in the source tree ([https://github.com/ghc/ghc/blob/master/docs/core-spec/core-spec.pdf PDF here]):

{{{
-G |-ty t1 : k
------------------------------ :: UnivCo
-G |-co t1 ==>!_R t2 : t1 ~R k t2
+G |-ty t1 : k1
+G |-ty t2 : k2
+isUnLiftedTy t1 = isUnLiftedTy t2
+(not (isUnLiftedTy t1)) \/ ((activeSizeTy t1 = activeSizeTy t2) /\ (isFloatingTy t1 = isFloatingTy t2))
+-------------------------------------------------------------------------------------- :: UnivCo
+G |-co t1 ==>!_R t2 : t1 ~R k2 t2
}}}

Basically it introduces three new predicates in UnivCo rule:

  1. Both types should be lifted or both types should be unlifted (Qnikst: note that original task forbids coercion between lifted and ''unboxed'')

  2. If types are unlifted then their ''size'' should be equal. For the meaning of ''size'' see "Size of value" section ('''SPJ''': what is "active size"? '''Qnikst''': I have generalized description so discussion about "active size" can be in one place)

  3. If types are unlifted then they either should be both floating or both integral

== Questions ==

There are few dark places in this semantics change that should be clarified

=== Size of value ===

'''SPJ''' I can't make head or tail of this section, and I am pretty sure that this section is all wrong.  But we need Geoff Mainland to help us out.  

I think that the difference between "active size" and "real size" (concepts whose very existence I dispute) is caused by `VecRep`, which in turn is Geoff Mainland's support for vector instructions; I think the most up to date description is [wiki:SIMD].  So we can have a type for a 4-vector of 32-bit quantities.  But these things may well be held in special registers, a bit like `Float`, and are probably not inter-coercible with anything else. 

For now, do something simple and conservative
 
'''End of SPJ'''


GHC has 2 different sizes: word aligned size of values, and active size in bytes that actually used.  '''SPJ''': where do you see these two different sizes in GHC's source code?

Term 'active size' is used to describe number of bytes that value actually use, at this moment such numbers are used
in Vectors, see `​primElemRepSizeB` in (source:compiler/types/TyCon.hs). The reasons about forbidding coercions between
values with a different active size is that in the rest bytes there will be garbage:

Hypothetical example for a Word16 on machine with 4-byte word size:
{{{
   [A|A|W|W]
    0 1 2 3 
}}}
the real size of this value will be 1 word (4 bytes), active size will be (2 bytes), bytes 2,3 will contain garbage.
So if we will check ''real size'' to coerce from Word16 to Word32, this coercion will be allowed, even if resulting
value will have no sense. 

However equality between `active size` looks like overestimation, as it's completely safe to coerce from larger value
(`Word32`) to lesser (`Word16`). ('''Qnikst''': is it really true in all cases signed/unsigned ints and floating values?)

The question is if we need to allow coercion between values with same word size, but different active size.
(Qnikst. current implementation forbids it, as values with different active size can contain garbage, however coercion from value with bigger active size to value with smaller potentially should be fine).

=== Unboxed Tuples ===

A big question is how to treat unboxed tuples if they have same size, can we coerce between `(# Int, Int64 #)` and `(# Int64, Int #)'?

'''SPJ''': I think it should be ok to coerce from `(# a, b #)` to `(# c,d #)` if it's safe to coerce from `a` to `c`, and ditto `b` to `d`.  The tuples must have the same length. 
'''Qnikst''': Do I understand correctly that it should not be possible to coerce `(# a, b, c #)` to `(# a, d #)` where `size b + size c == size d`?   '''SPJ''' Correct. Like I say "the tuples must have the same length".
 

How to check is value is floating in this case?  '''SPJ''' I don't understand the question.
'''Qnikst''': 'Coercion between unboxed ints and floats.' so we need to specify how it works for tuples.  '''SPJ''' I'm sorry I still don't understand what the issue is.  Can you just give a concrete example?

As far as I understand coerce from `(# a, b #)` to `(# c, d #)` should not be allowed if either `a`, `b` or `c`,`d` violates rule.
However if `(# a, b, c #)` could be converted to  `(# a, d #)` then what should be the rules for `b`, `c` -> `d`, as far as I understand
it's correct if `b`,`c`,`d`  are ints and not correct in any other case. 

(Qnikst. current implementation allow such coercions and doesn't check "floatiness" of values)

=== User programs ===

Should those check be applied only to internal GHC's transformations, for user programs should also be
checked?

'''SPJ''' Both ideally.  Emit a warning for uer programs with visible problems.  And check in Lint.  Start with the latter.

'''RAE''' So that means that a warning would be issued, followed by a !CoreLint failure. This violates the invariant that !CoreLint catches only GHC's mistakes. I loosely agree with this approach here (because I want to allow users to do terrible things if they really want to), but we'll have to be careful about wording the error message that !CoreLint spits out. This also implies that users who are actively trying to shoot themselves in the foot will have to avoid `-dcore-lint`, which is slightly dissatisfying. Maybe add a flag asking whether or not !CoreLint should perform these checks? I guess my tension stems from the fact that we want to protect most users from mistakes and want to detect mistakes in GHC, while still allowing crazy things to happen. (Like still exporting [https://github.com/haskell/bytestring/blob/2530b1c28f15d0f320a84701bf507d5650de6098/Data/ByteString/Internal.hs#L599 this function].)
 
'''SPJ''' Let's not make the best the enemy of the good.  Make the whole lot into warnings or something, if that would reassure you.  Mostly we are trying to identify smelly code; some of it might just possibly work.  On a particular processor, when the sun is shining.  

== Implementors ==

implementation: Alexander Vershilov / Qnikst

advisor: Richard Eisenberg / goldfire / RAE
```