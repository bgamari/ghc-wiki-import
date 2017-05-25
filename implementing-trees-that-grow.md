## Implementation of Trees that Grow



In this page, we discuss the overall plan and details of implementing [
Tress that Grow](http://www.jucs.org/jucs_23_1/trees_that_grow/jucs_23_01_0042_0062_najd.pdf) in GHC. 
The motivation and some background information can be found at the [
report](https://ghc.haskell.org/trac/ghc/wiki/NativeMetaprogramming) of a related Summer of Haskell project.



The long term goal is to use a single data type for


- GHC itself (currently `HsSyn`)
- Template Haskell (currently the types in the `template-haskell` library)
- `hs-src-exts`, a popular library for processing Haskell


The shorter term plan is to validate the idea by applying it to GHC.  That is, re-engineer `HsSyn` along the lines of Trees That Grow.



A major benefit is that we believe that this re-engineering will


- Completely subsume Alan Zimmerman's [
  Api Annotations](https://ghc.haskell.org/trac/ghc/wiki/ApiAnnotations), making them much easier to use.
- Allow us to get rid of the annoying alternation between `t` and `Located t`, which pervades `HsSyn`


Re-engineering `HsSyn` is a major exercise that touches a lot of code, so we need to move carefully.  This page outlines the plan.



Main protagonists: Shayan Najd and Alan Zimmerman. 


### General Plan



I have the following practical plan, which makes it possible to change the AST and the related code gradually, without touching the entire code base with a few git commits.
(I expect to fill in and update the details, as we go.)
It is done in four steps.


#### Step 0



We replace the current `HsSyn` with one using a type parameter that can enable the growable base AST, as proposed in [
https://phabricator.haskell.org/D3609](https://phabricator.haskell.org/D3609)


#### Step 1



We replace the current `HsSyn` with 


1. a growable base AST (following our latest design described in our JUCS paper); 
1. a set of extensions exactly as they are right now in GHC; and
1. a set of pattern synonyms as a (temporary) shim to avoid the need for changing the rest of the code base.


  
It will hopefully allow us to reuse the AST for Template Haskell (TH), or even refactoring the parser (to replace Haskell-Src-Exts, etc)   
At this stage, we WILL have some slow downs in GHC's own compile time (and possibly run time) due to the decomposition (and then recomposition), but we hopefully regain some speed by the next steps.
(Arguably the benefit of extensibility, by far, outweighs the \*tolerable\* slow downs)


#### Step 2



We reorganise the the way source locations are stored, by moving from the current alternating design to a direct style (as discussed in the Summer of Haskell project).



It will give us a cleaner parser (design/code wise), and speed ups.


#### Step 3



Possibly, we remove the pattern synonyms to avoid a layer of indirection and get some speed up. 


#### Step 4



We work on refactoring, by then redundant, bits and pieces of TH by either just removing them (like the HsSyn-TH translator) or reusing the ones in the compiler.


### Experiment



There is an experimental implementation at [
https://github.com/alanz/ghc/tree/wip/new-tree-one-param](https://github.com/alanz/ghc/tree/wip/new-tree-one-param).



The intention is to


1. replace the current `hsSyn` parameter which is one of `RdrName`, `Name` or `Id` for the output of the *parser*, *renamer* or *typechecker* respectively with an explicit parameter for the pass; and

1. add extension points to `HsLit.hs` to get a feel for how it will impact things.


 
It does this by adding a `HsExtension` module to `hsSyn`, defining as


```
data GhsPs -- Parser phase
data GhcRn -- Renamer
data GhcTc -- Typechecker
data GhcTh -- Template Haskell. Currently unused
```


This is a deviation from the *Trees that Grow* paper ([
http://www.jucs.org/jucs\_23\_1/trees\_that\_grow/jucs\_23\_01\_0042\_0062\_najd.pdf](http://www.jucs.org/jucs_23_1/trees_that_grow/jucs_23_01_0042_0062_najd.pdf)) section 4.2 which suggests


```
data GHC (c :: Component)
data Component = Compiler Pass | TemplateHaskell
data Pass = Parsed | Renamed | Typechecked
```


The deviation is due to a current problem in the implementation which requires the tag type to appear in the `hsSyn` AST types, and the requirement for `Data` instances for it.  See below for details.



This is not important for the experiment however, as in practice we would define type synonyms of the form


```
type GhcPs = GHC '(Compiler Parsed)
type GhcRn = GHC '(Compiler Renamed)
...
```


The `HsLit` module is amended as


```
data HsLit x
  = HsChar (XHsChar x) {- SourceText -} Char
      -- ^ Character
  | HsCharPrim (XHsCharPrim x) {- SourceText -} Char
      -- ^ Unboxed character
...
  | HsDoublePrim (XHsDoublePrim x) FractionalLit
      -- ^ Unboxed Double
```


Each constructor gets its own tag type, derived mechanically from the constructor name for ease of reference when being used.  The extension point is used for the `SourceText` where this is needed. But note that it is also added to constructors without `SourceText`, such as `HsDoublePrim`.



We then define the type families in `HsExtension.hs` for each extension point, as


```
type family XHsChar x
type family XHsCharPrim x
...
type family XHsDoublePrim x
```


For each compiler pass we define the specific mappings


```
-- GHCP
type instance XHsChar       GhcPs = SourceText
type instance XHsCharPrim   GhcPs = SourceText
...
type instance XHsDoublePrim GhsPc = ()

-- GHCR
type instance XHsChar       GhcRn = SourceText
type instance XHsCharPrim   GhcRn = SourceText
...
type instance XHsDoublePrim GhcRn = ()

...
```


These are all the same at the moment, and `()` is used for points not requiring anything for any of the passes.



One of the eventual goals (for \@alanz anyway) is to be able to pass an AST using different annotations on it to later passes of the compiler.



To facilitate this, some type classes are defined for the `SourceText` and providing initial/default values.


```
class HasSourceText a where
  -- Provide setters to mimic existing constructors
  noSourceText  :: a
  sourceText    :: String -> a

  setSourceText :: SourceText -> a
  getSourceText :: a -> SourceText

-- Named constraint to simplify usage
type SourceTextX x =
  ( HasSourceText (XHsChar x)
  , HasSourceText (XHsCharPrim x)
  ...
  )

```

```
class HasDefault a where
  def :: a

-- Named constraint to simplify usage
type HasDefaultX x =
  ( HasDefault (XHsChar x)
  , HasDefault (XHsCharPrim x)
...
  , HasDefault (XHsDoublePrim x)
  )
```


These have the expected instances for the two types used in GHC


```
instance HasSourceText SourceText where
  noSourceText    = NoSourceText
  sourceText s    = SourceText s

  setSourceText s = s
  getSourceText a = a

```

```
instance HasDefault () where
  def = ()

instance HasDefault SourceText where
  def = NoSourceText
```

#### PostXXX types



The paper also proposes explicitly using extension points for the `PostRn` and `PostTc` usages. This has not been done in the current experiment, which has the limited goals set out above. The types have been replaced with updated ones parameterised by the pass variable though


```
type family PostTC x ty -- Note [Pass sensitive types]
type instance PostTc GhcPs ty = PlaceHolder
type instance PostTc GhcRn ty = PlaceHolder
type instance PostTc GhcTc ty = ty

-- | Types that are not defined until after renaming
type family PostRN x ty  -- Note [Pass sensitive types]
type instance PostRn GhcPs ty = PlaceHolder
type instance PostRn GhcRn ty = ty
type instance PostRn GhcTc ty = ty
```

#### When we actually *need* a specific id type ===



Many functions and data types need to refer to variables that used to be simply the AST type parameter.  This ability is provided through the `IdP` type family


```
-- Maps the "normal" id type for a given pass
type family IdP p
type instance IdP GhcPs = RdrName
type instance IdP GhcRn = Name
type instance IdP GhcTc = Id
```


So we end up with


```
data Sig pass
    TypeSig
       [Located (IdP pass)]  -- LHS of the signature; e.g.  f,g,h :: blah
       (LHsSigWcType pass)   -- RHS of the signature; can have wildcards
...
```

#### Experiences



Once the `hsSyn` AST was converted, the conversion process for other modules is straightforward, as it is basically mapping


- `RdrName` to `GhcPs`
- `Name` to `GhcRn`
- `Id`  to `GhcTc`


in type signatures, and making sure that where the original was used "as is" to now wrap it in `IdP`.



In some cases adding `SourceTextX` or `HasDefaultX` constraints is also required. 


#### Problems



The `Data` instance for the index type is required due to the following kind of construct


```
  | ValBindsOut
        [(RecFlag, LHsBinds idL)]
        [LSig GHCR] -- AZ: how to do this?
```


This has `GHCR` as the hard coded index type for `LSig`.



I presume this can be dealt with by either


1. Somehow adding a constraint that `IdP idL ~ Name` (where `idL` is the AST index type; or

1. Making `ValBindsOut` only appear as a constructor when the pass type is `GHCR`, or has the `IdP idL ~ Name` constraint.


Can this be done? How?


#### Further steps


1. Implement the `PostRn` and `PostTc` mechanism as per *Trees that Grow*.

1. Sort out the `Data` problem for the parameter type.

1. Add further extension points to the AST.

1. Use of type synonyms, if required.


 

