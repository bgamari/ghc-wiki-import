# Types in the back end (aka "The `Rep` swamp")



I have completed a major representation change, affecting both old and new code generators, of the  various `Rep` types.  It's pervasive in that it touches a lot of files; and in the native code-gen very many lines are changed.  The new situation is much cleaner.



Here are the highlights of the new design.


## `CmmType`



There is a new type `CmmType`, defined in module `CmmExpr`, which is just what it sounds like: it's the type of a `CmmExpr` or a `CmmReg`.  


- A `CmmType` is *abstract*: its representation is private to `CmmExpr`.  That makes it easy to change representation.
- A `CmmType` is actually just a pair of a `Width` and a category (`CmmCat`).
- The `Width` type is exported and widely used in pattern-matching, but it does what it says on the tin: width only.  
- In contrast, the `CmmCat` type is entirely private to `CmmExpr`.  It is just an enumeration that allows us to distinguish: floats, gc pointers, and other. 


Other important points are these:


- Each `LocalReg` has a `CmmType` attached; this replaces the previous unsavoury combination of `MachRep` and `CmmKind`.  Indeed, both of the latter are gone entirely.

- Notice that a `CmmType` accurately knows about gc-pointer-hood. Ultimately we will abandon static-reference-table generation in STG syntax, and instead generate SRTs from the Cmm code.  We'll need to update the RTS `.cmm` files to declare pointer-hood.

- The type `TyCon.PrimRep` remains; it enumerates the representations that a Haskell value can take.  Differences from `CmmType`:

  - `PrimRep` contains `VoidRep`, but `CmmType` has no zero-width form.
  - `CmmType` includes sub-word width values (e.g. 8-bit) which `PrimRep` does not.
    The function `primRepCmmType` converts a non-void `PrimRep` to a `CmmType`.

- `CmmLint` is complains if you assign a gc-ptr to a non-gc-ptr and vice versa.  It treats "gc-ptr + constant" as a gc-ptr.  

>
> >
> >
> > *NB: you'd better not make an interior pointer live across a call*, else we'll save it on the stack and treat it as a GC root.  It's not clear how to guarantee this doesn't happen as the result of some optimisation.
> >
> >
>


**Parsing `.cmm` RTS files.**  The global register `P0` is a gc-pointer version of `R0`.  They both map to the same physical register, though!


## The `MachOp` type



The `MachOp` type enumerates (in machine-independent form) the available machine instructions.  The principle they embody is that *everything except the width is embodied in the opcode*.  In particular, we have


- `MO_S_Lt`, `MO_U_Lt`, and `MO_F_Lt` for comparison (signed, unsigned, and float).
- `MO_SS_Conv`, `MO_SF_Conv` etc, for conversion (`SS` is signed-to-signed, `SF` is signed-to-float, etc).


These constructor all take `Width` arguments.



The `MachOp` data type is defined in `CmmExpr`, not in a separate `MachOp` module.


## Foreign calls and hints



In the new Cmm representation (`ZipCfgCmmRep`), but not the old one, arguments and results to all calls, including foreign ones, are ordinary `CmmExpr` or `CmmReg` respectively.  The extra information we need for foreign calls (is this signed?  is this an address?) are kept in the calling convention.  Specifically:


- `MidUnsafeCall` calls a `MidCallTarget`
- `MidCallTarget` is either a `CallishMachOp` or a `ForeignTarget`
- In the latter case we supply a `CmmExpr` (the function to call) and a `ForeignConvention`
- A `ForeignConvention` contains the C calling convention (stdcall, ccall etc), and a list of `ForiegnHints` for arguments and for results. (We might want to rename this type.)


This simple change was horribly pervasive.  The old Cmm rep (and Michael Adams's stuff) still has arguments and results being (argument,hint) pairs, as before.


## Native code generation and the `Size` type



The native code generator has an instruction data type for each architecture.  Many of the instructions in these data types used to have a `MachRep` argument, but now have a `Size` argument instead.  In fact, so far as the native code generators are concerned, these `Size` types (which can be machine-specific) are simply a plug-in replacement for `MachRep`, with one big difference: **`Size` is completely local to the native code generator** and hence can be changed at will without affecting the rest of the compiler.



`Size` is badly named, but I inherited the name from the previous code.



I rather think that many instructions should have a `Width` parameter, not a `Size` parameter.  But I didn't feel confident to change this.  Generally speaking the NCG is a huge swamp and needs re-factoring.


