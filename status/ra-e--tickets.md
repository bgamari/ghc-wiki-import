# Tickets that Richard E. is interested in


## Type system



**Easy**


- [\#7494](https://gitlab.staging.haskell.org/ghc/ghc/issues/7494): Allow type synonyms in GADT return types
- [\#8109](https://gitlab.staging.haskell.org/ghc/ghc/issues/8109): As-patterns in type patterns
- [\#8634](https://gitlab.staging.haskell.org/ghc/ghc/issues/8634): Dysfunctional dependencies
- [\#9636](https://gitlab.staging.haskell.org/ghc/ghc/issues/9636): Should `F Bool` be well-formed, if `F` is an empty closed type family? (blocked by [\#9637](https://gitlab.staging.haskell.org/ghc/ghc/issues/9637))
- [\#9687](https://gitlab.staging.haskell.org/ghc/ghc/issues/9687): Need `Typeable (,,,,,,,,,,)` and friends
- [\#10116](https://gitlab.staging.haskell.org/ghc/ghc/issues/10116): Warn on incomplete closed type families
- [\#10756](https://gitlab.staging.haskell.org/ghc/ghc/issues/10756): Impossible patterns
- [\#10789](https://gitlab.staging.haskell.org/ghc/ghc/issues/10789): Warn about type families stuck on kinds
- [\#11620](https://gitlab.staging.haskell.org/ghc/ghc/issues/11620): Kind signatures on classes. But with what syntax?
- [\#11977](https://gitlab.staging.haskell.org/ghc/ghc/issues/11977): Function as pattern return type


**Medium**


- [\#3483](https://gitlab.staging.haskell.org/ghc/ghc/issues/3483): Notation for known-inaccessible code, like `()` in Agda
- [\#6018](https://gitlab.staging.haskell.org/ghc/ghc/issues/6018): Injective type families (Jan is working on)
- [\#7296](https://gitlab.staging.haskell.org/ghc/ghc/issues/7296): Incoherent instance lookup is allowed within an instance declaration (by design)

  - [\#9820](https://gitlab.staging.haskell.org/ghc/ghc/issues/9820): Another example of the same behavior
- [\#7495](https://gitlab.staging.haskell.org/ghc/ghc/issues/7495): Allowing list syntax for `HList`
- [\#8128](https://gitlab.staging.haskell.org/ghc/ghc/issues/8128): Derived instances sometimes have inaccessible code
- [\#8165](https://gitlab.staging.haskell.org/ghc/ghc/issues/8165): GND should make associated types, too (not very well specified)
- [\#8388](https://gitlab.staging.haskell.org/ghc/ghc/issues/8388): Have a consistent story around non-`*` types in a forall
- [\#9180](https://gitlab.staging.haskell.org/ghc/ghc/issues/9180): Compile-time `staticError` function; seems easy, but I don't know how to do this.
- [\#9427](https://gitlab.staging.haskell.org/ghc/ghc/issues/9427): Break cycles in recursive class/type definitions (the second half of the fix for [\#9200](https://gitlab.staging.haskell.org/ghc/ghc/issues/9200))
- [\#9547](https://gitlab.staging.haskell.org/ghc/ghc/issues/9547): Better inference for whether `() :: *` or `() :: Constraint`
- [\#9637](https://gitlab.staging.haskell.org/ghc/ghc/issues/9637): Type-level `Error` that aborts compilation
- [\#9649](https://gitlab.staging.haskell.org/ghc/ghc/issues/9649): Proper type-level strings
- [\#9667](https://gitlab.staging.haskell.org/ghc/ghc/issues/9667): Don't make tyvars untouchable when a GADT pattern-match isn't informative
- [\#9883](https://gitlab.staging.haskell.org/ghc/ghc/issues/9883): Heterogeneous `OverloadedLists`
- [\#10114](https://gitlab.staging.haskell.org/ghc/ghc/issues/10114): Non-`*` bodies of foralls
- [\#10318](https://gitlab.staging.haskell.org/ghc/ghc/issues/10318): Allow superclass cycles

  - [\#10592](https://gitlab.staging.haskell.org/ghc/ghc/issues/10592): Allow superclass cycles
- [\#10581](https://gitlab.staging.haskell.org/ghc/ghc/issues/10581): Recommend ScopedTypeVariables
- [\#10836](https://gitlab.staging.haskell.org/ghc/ghc/issues/10836): Continue reporting errors between mut. rec. groups
- [\#11113](https://gitlab.staging.haskell.org/ghc/ghc/issues/11113): Semantics for type-level reduction
- [\#11339](https://gitlab.staging.haskell.org/ghc/ghc/issues/11339): Why do pattern bindings always infer types?
- [\#11342](https://gitlab.staging.haskell.org/ghc/ghc/issues/11342): The `Char` kind
- [\#11715](https://gitlab.staging.haskell.org/ghc/ghc/issues/11715): `Constraint` vs `*`


**Hard**


- [\#8828](https://gitlab.staging.haskell.org/ghc/ghc/issues/8828): Type pattern synonyms
- [\#10227](https://gitlab.staging.haskell.org/ghc/ghc/issues/10227): Backward reasoning from closed type families
- [\#11511](https://gitlab.staging.haskell.org/ghc/ghc/issues/11511): Think Hard about non-terminating injective type families
- [\#11962](https://gitlab.staging.haskell.org/ghc/ghc/issues/11962): Induction recursion


**Rocket Science**


- [\#1965](https://gitlab.staging.haskell.org/ghc/ghc/issues/1965): Allow existentials in newtypes
- [\#2256](https://gitlab.staging.haskell.org/ghc/ghc/issues/2256): Quantify over implication constraints
- [\#4259](https://gitlab.staging.haskell.org/ghc/ghc/issues/4259): Allow recursive checks for compatibility in type families

  - [\#8423](https://gitlab.staging.haskell.org/ghc/ghc/issues/8423): Ditto for closed type families (may have different solution)
- [\#7259](https://gitlab.staging.haskell.org/ghc/ghc/issues/7259): Eta expansion of products
- [\#7961](https://gitlab.staging.haskell.org/ghc/ghc/issues/7961): Implement "nokinds" (RAE is working on!)

  - [\#9017](https://gitlab.staging.haskell.org/ghc/ghc/issues/9017): Bad error message b/c of missing kind equality
  - [\#10379](https://gitlab.staging.haskell.org/ghc/ghc/issues/10379): Can't parse prefix `[]` in kind
- [\#8338](https://gitlab.staging.haskell.org/ghc/ghc/issues/8338): Incoherent instances without `-XIncoherentInstances`

  - [\#2356](https://gitlab.staging.haskell.org/ghc/ghc/issues/2356): Strangeness about GHC's lazy overlap check
- [\#9429](https://gitlab.staging.haskell.org/ghc/ghc/issues/9429): An alternative to `Any`. For example, we want `Typeable (forall x. x -> x)`.
- [\#9562](https://gitlab.staging.haskell.org/ghc/ghc/issues/9562): Type families + hs-boot files = `unsafeCoerce`
- [\#10327](https://gitlab.staging.haskell.org/ghc/ghc/issues/10327): Closed type families should reduce regardless of infinite types
- [\#10514](https://gitlab.staging.haskell.org/ghc/ghc/issues/10514): `Generic` for existentials

## Typechecker


- [\#8095](https://gitlab.staging.haskell.org/ghc/ghc/issues/8095): More type-families optimizations, dropping coercions without `-dcore-lint`
- [\#9450](https://gitlab.staging.haskell.org/ghc/ghc/issues/9450): Interleave checking against an hs-boot file while typechecking definitions
- [\#9557](https://gitlab.staging.haskell.org/ghc/ghc/issues/9557): Deriving instances is slow
- [\#10141](https://gitlab.staging.haskell.org/ghc/ghc/issues/10141): Add a hint about CUSKs to relevant error messages
- [\#10361](https://gitlab.staging.haskell.org/ghc/ghc/issues/10361): Make `DeriveAnyClass` work with associated type defaults
- [\#10381](https://gitlab.staging.haskell.org/ghc/ghc/issues/10381): RebindableSyntax and RankNTypes
- [\#10808](https://gitlab.staging.haskell.org/ghc/ghc/issues/10808): Type families and record updates

## Roles & such



**More/better roles**


- [\#8177](https://gitlab.staging.haskell.org/ghc/ghc/issues/8177): Roles for type families (RAE owns, but is *not* working on)
- [\#9112](https://gitlab.staging.haskell.org/ghc/ghc/issues/9112): GND with `Vector`/`MVector`
- [\#9118](https://gitlab.staging.haskell.org/ghc/ghc/issues/9118): No eta-reduction possible
- [\#9123](https://gitlab.staging.haskell.org/ghc/ghc/issues/9123): Need higher-kinded roles


**Error messages**


- [\#9518](https://gitlab.staging.haskell.org/ghc/ghc/issues/9518)

## Front end


- [\#7169](https://gitlab.staging.haskell.org/ghc/ghc/issues/7169): Warn about incomplete record selectors
- [\#7668](https://gitlab.staging.haskell.org/ghc/ghc/issues/7668): Better locations in deferred type errors
- [\#9376](https://gitlab.staging.haskell.org/ghc/ghc/issues/9376): Improve error messages for closed type families that get stuck on the dark corners
- [\#9378](https://gitlab.staging.haskell.org/ghc/ghc/issues/9378): Make unknown LANGUAGE pragmas warnings
- [\#9394](https://gitlab.staging.haskell.org/ghc/ghc/issues/9394): `:info` should show instances of data and type families.
- [\#9784](https://gitlab.staging.haskell.org/ghc/ghc/issues/9784): Report better error for `Foo.'Z`
- [\#10056](https://gitlab.staging.haskell.org/ghc/ghc/issues/10056): Remove the magic from parsing `~`. Some open design questions.
- [\#12477](https://gitlab.staging.haskell.org/ghc/ghc/issues/12477): Left-sections in types


**Medium**


- [\#7401](https://gitlab.staging.haskell.org/ghc/ghc/issues/7401): Derive `Eq` and friends for empty datatypes


**Design needed**


- [\#7870](https://gitlab.staging.haskell.org/ghc/ghc/issues/7870): Customized error messages

## Optimizations


- [\#10906](https://gitlab.staging.haskell.org/ghc/ghc/issues/10906): Better `SPECIALISE instance`

## Template Haskell



**Easy**


- [\#6089](https://gitlab.staging.haskell.org/ghc/ghc/issues/6089): Allow nested declaration splices
- [\#7808](https://gitlab.staging.haskell.org/ghc/ghc/issues/7808): Allow reification of a data instance name
- [\#9022](https://gitlab.staging.haskell.org/ghc/ghc/issues/9022): Fix semicolons in pretty-printer
- [\#9699](https://gitlab.staging.haskell.org/ghc/ghc/issues/9699): Function to list all names in scope
- [\#10267](https://gitlab.staging.haskell.org/ghc/ghc/issues/10267): Add holes (jstolarek)
- [\#10385](https://gitlab.staging.haskell.org/ghc/ghc/issues/10385): Add extra check around TH annotations
- [\#10486](https://gitlab.staging.haskell.org/ghc/ghc/issues/10486): More `addTopDecls`
- [\#10572](https://gitlab.staging.haskell.org/ghc/ghc/issues/10572): Quantify over quoted variables
- [\#10603](https://gitlab.staging.haskell.org/ghc/ghc/issues/10603): Better parentheses in TH pretty-printer


**Unknown**


- [\#9693](https://gitlab.staging.haskell.org/ghc/ghc/issues/9693): Stale state left in failed compilation with GHCi and TH.
- [\#10279](https://gitlab.staging.haskell.org/ghc/ghc/issues/10279): Issue with package names
- [\#10330](https://gitlab.staging.haskell.org/ghc/ghc/issues/10330): Better locations in error messages


**Design needed**


- [\#1475](https://gitlab.staging.haskell.org/ghc/ghc/issues/1475): Adding imports/exports

  - [\#10391](https://gitlab.staging.haskell.org/ghc/ghc/issues/10391): Reify exports
- [\#1831](https://gitlab.staging.haskell.org/ghc/ghc/issues/1831): `reify` never provides variable info
- [\#4222](https://gitlab.staging.haskell.org/ghc/ghc/issues/4222): Reifying abstract types
- [\#5467](https://gitlab.staging.haskell.org/ghc/ghc/issues/5467): Haddock in TH
- [\#8679](https://gitlab.staging.haskell.org/ghc/ghc/issues/8679): Include value and function signatures in their declarations
- [\#8761](https://gitlab.staging.haskell.org/ghc/ghc/issues/8761): Pattern synonyms
- [\#10331](https://gitlab.staging.haskell.org/ghc/ghc/issues/10331): Better interop between TH and HsSyn
- [\#10541](https://gitlab.staging.haskell.org/ghc/ghc/issues/10541): Reifying tyvars' kinds

## Generic programming


- [\#8560](https://gitlab.staging.haskell.org/ghc/ghc/issues/8560): Generic representation for GADTs
- [\#10514](https://gitlab.staging.haskell.org/ghc/ghc/issues/10514): Generic representation for existentials

## Documentation


- [\#9247](https://gitlab.staging.haskell.org/ghc/ghc/issues/9247): Document `-XDatatypeContexts`
- [\#9248](https://gitlab.staging.haskell.org/ghc/ghc/issues/9248): Document `-X` extensions
- [\#9737](https://gitlab.staging.haskell.org/ghc/ghc/issues/9737): Document `ANN` in pragmas chapter

## Tasks


- Search for email with subject "Branched and unbranched" in my GHC folder -- about refactoring some `CoAxiom` stuff
- Simplification for axioms: they can be applied to types, not coercions. See email with subject "checkAxInstCo"
- Refactor the type-checking algorithm, à la [Typechecking](typechecking).
