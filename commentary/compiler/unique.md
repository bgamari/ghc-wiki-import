CONVERSION ERROR

Original source:

```trac
== Unique ==

`Unique`s provide a fast comparison mechanism for more complex things. Every `RdrName`, `Name`, `Var`, `TyCon`, `TyVar`, etc. has a `Unique`. When these more complex structures are collected (in `UniqFM`s or other types of collection), their `Unique` typically provides the key by which the collection is indexed.

--------------------------
== Current design ==

A `Unique` consists of the ''domain'' of the thing it identifies and a unique integer value 'within' that domain. The two are packed into a single `Int#`, with the ''domain'' being the top 8 bits.

The domain is never inspected (SLPJ believes).  The sole reason for its existence is to provide a number of different ranges of `Unique` values that are guaranteed not to conflict.

=== Lifetime

The lifetime of a `Unique` is a single invocation of GHC, i.e. they must not 'leak' to compiler output, the reason being that `Unique`s may be generated/assigned non-deterministically. When compiler output is non-deterministic, it becomes significantly harder to, for example, [wiki:Commentary/Compiler/RecompilationAvoidance avoid recompilation]. Uniques do not get serialised into .hi files, for example.

Note, that "one compiler invocation" is not the same as the compilation of a single `Module`. Invocations such as `ghc --make` or `ghc --interactive` give rise to longer invocation life-times. 

This is also the reasons why `OccName`s are ''not'' ordered based on the `Unique`s of their underlying `FastString`s, but rather ''lexicographically'' (see [[GhcFile(compiler/basicTypes/OccName.lhs)]] for details).
> > '''SLPJ:''' I am far from sure that the Ord instance for `OccName` is ever used, so this remark is probably misleading.  Try deleting it and see where it is used (if at all).
> '''PKFH:''' At least `Name` and `RdrName` (partially) define their own `Ord` instances in terms of the instance of `OccName`. Maybe these `Ord` instances are also redundant, but for now it seems wise to keep them in. When everything has `Data` instances (after this and many other redesigns), I'm sure it will be easier to find such dependency relations.


=== Known-key things ===

A hundred or two library entities (types, classes, functions) are so-called "known-key things". See [wiki:Commentary/Compiler/WiredIn this page].  A known-key thing has a fixed `Unique` that is fixed when the compiler is built, and thus lives across all invocations of that compiler.  These known-key `Unique`s ''are'' written into .hi files.  But that's ok because they are fully deterministic and never change.

> '''PKFH''' That's fine then; we also know for sure these things fit in the 30 bits used in the `hi`-files. I'll comment appropriately.

=== Interface files ===

Entities in a interface file (.hi file) are, for the most part, stored in a symbol table, and referred to (from elsewhere in the same interface file) by an index into that table.  Here are the details from [[GhcFile(compiler/iface/BinIface.lhs)]]:
{{{
-- Note [Symbol table representation of names]
-- ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
--
-- An occurrence of a name in an interface file is serialized as a single 32-bit word.
-- The format of this word is:
--  00xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
--   A normal name. x is an index into the symbol table
--  01xxxxxxxxyyyyyyyyyyyyyyyyyyyyyyyy
--   A known-key name. x is the Unique's Char, y is the int part
--  10xxyyzzzzzzzzzzzzzzzzzzzzzzzzzzzz
--   A tuple name:
--    x is the tuple sort (00b ==> boxed, 01b ==> unboxed, 10b ==> constraint)
--    y is the thing (00b ==> tycon, 01b ==> datacon, 10b ==> datacon worker)
--    z is the arity
--  11xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
--   An implicit parameter TyCon name. x is an index into the FastString *dictionary*
--
-- Note that we have to have special representation for tuples and IP TyCons because they
-- form an "infinite" family and hence are not recorded explicitly in wiredInTyThings or
-- basicKnownKeyNames.
}}}


----------------------
== Redesign (2014) ==

=== TL;DR
The redesign is to accomplish the following:
 * Allow derivation of type class instances for `Unique`
 * Restore invariants from the original design; hide representation details
 * Eliminate violations of invariants and design-violations in other places of the compiler (e.g. `Unique`s shouldn't be written to `hi`-files, but are).
> > '''SLPJ''' I don't think this is a design violation; see above.  Do you have any other examples in mind?
> '''PKFH''' Not really of design-violations (and no other compiler-output stuff) other than the invariants mentioned above it, just yet. The key point, though, is that there are a lot of comments in `Unique` about not exporting things so that we know X, Y and Z, but then those things ''are'' exported, so we don't know them to be true. Case in point is the export of `mkUnique`, but also `mkUniqueGrimily`. The latter has a comment 'only for `UniqSupply`' but is also used in other places (like Template Haskell). One redesign is to put this restriction in the name, so there still is the facility offered by `mkUniqueGrimily`, but now it's called `mkUniqueOnlyForUniqSupply` (and `mkUniqueOnlyForTemplateHaskell`), the ugliness of which should help, over time, to get rid of them.

=== Longer

In an attempt to give more of GHC's innards well-behaved instances of `Typeable`, `Data`, `Foldable`, `Traversable`, etc. the implementation of `Unique`s was a bit of a sore spot. They were implemented (20+ years earlier) using custom boxing, viz.
{{{
data Unique = MkUnique Int#
}}}
making automatic derivation of such type class instances hard. There was already a comment asking why it wasn't simply a `newtype` around a normal (boxed) `Int`. Independently, there was some discussion on the mailinglists about the use of (signed) `Int`s in places where `Word`s would be more appropriate. Further inspection of the `Unique` implementation made clear that a lot of invariants mentioned in comments had been violated by incremental edits. This is discussed in more detail below, but these things together (the desire for automatic derivation and the restoration of some important invariants) motivated a moderate redesign.


=== Status Quo (pre redesign)

A `Unique` has a domain (`TyCon`, `DataCon`, `PrelName`, `Builtin`, etc.) that was codified by a character. The remainder of the `Unique` was an integer that should be unique for said domain. This '''was''' once guaranteed through the export list of [[GhcFile(compiler/basicTypes/Unique.lhs)]], where direct access to the domain-character was hidden, i.e.
{{{
mkUnique :: Char -> Int -> Unique
unpkUnique :: Unique -> (Char,Int)
}}}
were not exported. This should have guaranteed that every domain was assigned its own unique character, because only in [[GhcFile(compiler/basicTypes/Unique.lhs)]] could those `Char`s be assigned. However, through
{{{
mkUniqueGrimily :: Int -> Unique
mkUniqueGrimily i = MkUnique (iUnbox i)
}}}
this separation of concerns leaked out to [[GhcFile(compiler/basicTypes/UniqSupply.lhs)]], because its `Int` argument is the ''entire'' `Unique` and not just the integer part 'under' the domain character.
> > '''SLPJ''' OK, but to eliminate `mkUniqueGrimily` you need to examine the calls, decide how to do it better, and document the new design.
> '''PKFH''' See above; the solution for now is `mkUniqueOnlyForUniqSupply`. A separate patch will deal with trying to refactor/redesign `UniqSupply` if this is necessary.

The function `mkSplitUniqSupply` made the domain-character accessible to all the other modules, by having a wholly separate implementation of the functionality of `mkUnique`.

Where the intention was still to have a clean interface, the (would-be) hidden `mkUnique` is only called by functions defined in the `Unique` module with the corresponding character, e.g.
{{{
mkAlphaTyVarUnique   i = mkUnique '1' i
mkPreludeClassUnique i = mkUnique '2' i
mkPreludeTyConUnique i = mkUnique '3' (3*i)
...
}}}


=== New plan

In the new design, the domains are explicitly encoded in a sum-type `UniqueDomain`. At the very least, this should help make the code a little more self-documenting ''and'' prevent accidental overlap in the choice of bits to identify the domain. Since the purpose of `Unique`s is to provide ''fast'' comparison for different types of things, the redesign should remain performance concious. With this in mind, keeping the `UniqueDomain` and the integer-part explicitly in the type
{{{
data Unique = MkUnique UniqueDomain Word
}}}
seems unwise, but by choosing
{{{
newtype Unique = MkUnique Word
}}}
we win the ability to automatically derive things and should also be able to test how far optimisation has come in the past 20+ years; does default boxing with `newtype`-style wrapping have (nearly) the same performance as manual unboxing? This should follow from the tests.

The encoding is kept the same, i.e. the `Word` is still built up with the domain encoded in the most significant bits and the integer-part in the remaining bits. However, instead encoding the domain as a `Char` in the (internal ''and'' external interface), we now create an ADT (sum-type) that encodes the domain. This has two advantages. First, it prevents people from picking domain-tags ad hoc an possibly overlapping. Second, encoding in the `Word` does not rely on the assumption that the domain requires and/or fits in 8 bits. Since Haskell `Char`s are unicode, the 8-bit assumption is wrong for the old design. In other words, the above examples are changed to:

{{{
data UniqueDomain
  = AlphaTyVar
  | PreludeClass
  | PreludeTyCon
  ...
  deriving (Enum,Bounded)

domSiz :: Int  -- The size of domain in the encoded Unique. *NOT* exported, but change-safe and compile-time constant.
domSiz = ceiling $ logBase 2 $ fromIntegral $ fromEnum (maxBound :: UniqueDomain) - fromEnum (minBound::UniqueDomain) + 1


mkUnique :: UniqueDomain -> Int -> Unique -- *Can* be exported now, but all those helper functions are gone.
}}}

Ideal world scenario, the entire external interface would be:
{{{
 UniqueDomain(..)
 mkUnique    :: UniqueDomain -> Word -> Unique
 pprUnique   :: Unique -> SDoc
 showUnique  :: Unique -> String
 serialise   :: Word8   -- number of bits to keep for other encoding
             -> Unique  -- the thing to serialise
             -> Word32  -- the serialised representation (for BinIface)
 deserialise :: Word 8 -> Word32 -> Unique
}}}
and the instances for `Eq`, `Ord`, `Data`, etc. For now, though, it will also have
{{{
 getKey :: Unique -> Int
 mkUniqueOnlyForUniqSupply :: Int -> Unique
 mkUniqueOnlyForTemplateHaskell :: FastInt -> Unique
 incrUnique :: Unique -> Unique
 deriveUnique :: Unique -> Int -> Unique
 newTagUnique :: Unique -> UniqueDomain -> Unique
}}}

> > '''SLPJ''' I agree that a `newtype` around a `Word` is better than a `data` type around `Int#`. That is a small, simple change.  But I think you plan to do more than this, and that "more" is not documented here.  E.g. what is the new API to `Unique`?
> '''PKFH''' Added. See above.


```
