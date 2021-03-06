


# `DuplicateRecordFields`



This page describes the `DuplicateRecordFields` extension, part 1 of the [OverloadedRecordFields proposal](records/overloaded-record-fields). This was implemented as [
Phab:D761](https://phabricator.haskell.org/D761) and [
Phab:D1391](https://phabricator.haskell.org/D1391) and **will be in GHC 8.0**.


## Design



The `DuplicateRecordFields` extension permits existing Haskell records to use duplicate field labels.  Thus the following is legal in a single module:


```wiki
data Person  = MkPerson  { personId :: Int, name :: String }
data Address = MkAddress { personId :: Int, address :: String }
```


Without the extension, this module will not compile because it tries to generate two selector functions called `personId`. When the extension is enabled, both selector functions are generated, and the renamer will determine which is meant at use sites.


### Selector functions



Bare uses of the field refer only to the selector function, and work only if this is unambiguous.  Thus, with the above example definitions we can write


```wiki
x p = name p
```


but bare use of `personId` leads to a name resolution error, e.g. in


```wiki
y p = personId p
```


We can use the type being pushed in to the occurrence of the selector, or a type signature on its argument, to determine the datatype that is meant. For example, the following are permitted:


```wiki
f = personId :: Person -> Int

g :: Address -> Int
g = personId

z = personId (p :: Person)
```


However, we do not infer the type of the argument to determine the datatype, or have any way of deferring the choice to the constraint solver. Thus the following is ambiguous:


```wiki
bad (p :: Person) = personId p
```


Even though a field label is duplicated in its defining module, it may be possible to use the selector unambiguously elsewhere. For example, another module could import `Person(personId)` but not `Address(personId)`, and then use `personId` unambiguously. Thus it is not enough simply to avoid generating selector functions for duplicated fields.


### Construction and pattern-matching



Uses of fields that are always unambiguous because they mention the constructor, including construction and pattern-matching, may freely use duplicated names.  For example, the following are permitted with both `Person(personId)` and `Address(personId)` in scope:


```wiki
a = MkPerson { personId = 1, name = "Julius" }

f (MkPerson{personId = i}) = i
```


Turning on `DuplicateRecordFields` automatically enables `DisambiguateRecordFields`, because the former strictly generalises the latter.


### Disambiguating record updates



In a record update such as `e { personId = 1 }`, if there are multiple `personId` fields in scope, the type of the context must fix which record datatype is intended, or a type annotation must be supplied. While we require record updates to determine a single unambiguous record type, we can be slightly more liberal than Haskell 98 in how we determine that record type. Consider the following definitions:


```wiki
data S = MkS { foo :: Int }
data T = MkT { foo :: Int, bar :: Int }
data U = MkU { bar :: Int, baz :: Int }
```


Previously, an update mentioning `foo` would automatically be ambiguous if all these definitions were in scope. With `DuplicateRecordFields`, however, we can try the following:


1. Check for types that have all the fields being updated. For example:

  ```wiki
  f x = x { foo = 3, bar = 2 }
  ```

  Here `f` must be updating `T` because neither `S` nor `U` have
  both fields. This may also discover that no possible type exists.
  For example the following will be rejected:

  ```wiki
  f' x = x { foo = 3, baz = 3 }
  ```

1. Use the type being pushed in, if it is an application of a type constructor. The following are valid updates to `T`:

  ```wiki
  g :: T -> T
  g x = x { foo = 3 }

  g' x = x { foo = 3 } :: T
  ```

1. Use the type signature of the record expression, if it exists and is an application of a type constructor. Thus this is valid update to `T`:

  ```wiki
  h x = (x :: T) { foo = 3 }
  ```


Note that we do not look up the types of variables being updated, and no constraint-solving is performed, so for example the following will be rejected as ambiguous:


```wiki
let x :: T
    x = blah
in x { foo = 3 }

\x -> [x { foo = 3 },  blah :: T ]

\ (x :: T) -> x { foo = 3 }
```


We could add further tests, of a more heuristic nature. For example, rather than looking for an explicit signature, we could try to infer the type of the record expression, in case we are lucky enough to get an application of a type constructor straight away. However, it might be hard for programmers to predict whether a particular update is sufficiently obvious for the signature to be omitted.


### Import and export of record fields



When `DuplicateRecordFields` is enabled, an ambiguous field must be exported as part of its datatype, rather than at the top level. For example, the following is legal:


```wiki
module M ( Person(personId), Address(..) ) where
data Person  = MkPerson  { personId :: Int, name :: String }
data Address = MkAddress { personId :: Int, address :: String }
```


However, this would not be permitted, because `personId` is ambiguous:


```wiki
module M (personId) where ...
```


Similar restrictions apply on import.


## Implementation



When this extension is enabled, typechecking a record datatype still generates record selectors, but their `Name`s have a `$sel` prefix and end with the name of their first data constructor. For example,


```wiki
data T = MkT { x :: Int }
```


generates


```wiki
$sel:x:MkT :: T -> Int -- record selector (used to be called `x`)
$sel:x:MkT (MkT x) = x
```


This allows the same field label to occur multiple times in the same module, but with distinct `Name`s. Correspondingly, the renamer has to treat field labels specially, so that it finds `$sel:x:MkT` when looking up the `RdrName` `x`.


### `FieldLabel`



A field is represented by the following datatype, parameterised by the representation of names:


```wiki
data FieldLbl a = FieldLabel {
      flLabel        :: FieldLabelString, -- ^ Label of the field
      flIsOverloaded :: Bool, -- ^ DuplicateRecordFields enabled at definition site?
      flSelector     :: a,    -- ^ Record selector function
    }

type FieldLabelString = FastString
type FieldLabel = FieldLbl Name
```


For this purpose, a field "is overloaded" if it was defined in a module with `DuplicateRecordFields` enabled, so its selector name differs from its label. That is, it is irrelevant whether there are actually multiple identical field labels in the module. Every field has a label (`FastString`) and selector name. The `dcFields` field of `DataCon` stores a list of `FieldLabel`.



In interface files, the `ifConFields` field of `IfaceConDecl` stores a list of `IfaceTopBndr`s for selectors, and `IfaceConDecls` for datatypes/newtypes stores the field labels.



Why do we need the `flIsOverloaded` flag at all? Overloaded and non-overloaded fields are treated differently in various places, as non-overloaded fields can be thought of as exported at the top level of a module, whereas overloaded fields are exported only in the context of their parent type constructor. Notably, `availNames` returns the names of non-overloaded but not overloaded selectors. Moreover, Haddock needs to treat them differently.


### `AvailInfo` and `IE`



The new definition of `AvailInfo` is:


```wiki
data AvailInfo      = Avail Name | AvailTC Name [Name] [FieldLabel]
```


The `AvailTC` constructor represents a type and its pieces that are in scope. Record fields are now stored separately in the third argument. Similarly, the `IEThingWith (Located name) [Located name] [Located (FieldLbl name)]` constructor of `IE`, which represents a thing that can be imported or exported, also has a separate argument for fields.



Note that a `FieldLabelString` and parent is not enough to uniquely identify a selector, because of data families: if we have


```wiki
module M ( F (..) ) where
  data family F a
  data instance F Int { foo :: Int }

module N ( F (..) ) where
  import M ( F(..) )
  data instance F Char { foo :: Char }
```


then `N` exports two different selectors with the `FieldLabelString` `"foo"`. Similar tricks can be used to generate parents that have a mixture of overloaded and non-overloaded fields as children.


### `Parent` and `GlobalRdrElt`



The `Parent` type has an extra constructor `FldParent Name (Maybe FieldLabelString)` that stores the parent `Name` and the field label. The `GlobalRdrElt` (`GRE`) for a field stores the selector name directly, and uses the `FldParent` constructor to store the field. Thus a field `x` of type `T` gives rise this entry in the `GlobalRdrEnv`:


```wiki
x |->  GRE $sel:x:MkT (FldParent T (Just x)) LocalDef
```


Note that the `OccName` used when adding a GRE to the environment (`greOccName`) now depends on the parent field: for `FldParent` it is the field label, if present, rather than the selector name.


### Source expressions



An occurrence of a field is represented by the new datatype


```wiki
data FieldOcc name = FieldOcc RdrName (PostRn name name)
```


where the first component is the field name as written by the user (hence `RdrName`), and the second component (filled in by the renamer) is the name of the selector function.



The `HsExpr` type has an extra constructor `HsSingleRecFld (FieldOcc id)`. Regardless of whether `DuplicateRecordFields` is enabled, when `rnExpr` encounters `HsVar "x"` where `x` refers to an unambiguous record field `foo`, it replaces it with `HsSingleRecFld (FieldOcc "foo" $sel:foo:MkT)`. The point of this constructor is so we can pretty-print the field name, but store the selector name for typechecking.



Where an AST representation type (e.g. `HsRecField` or `ConDeclField`) contained an argument of type `Located id` for a field, it now stores a `Located (FieldOcc id)` aka `LFieldOcc id`. The new definition of `ConDeclField` (used in types) is:


```wiki
data ConDeclField name
  = ConDeclField { cd_fld_names :: [LFieldOcc name]
                   cd_fld_type  :: LBangType name, 
                   cd_fld_doc   :: Maybe LHsDocString }
```


The new definition of `HsRecField` is:


```wiki
type HsRecField id arg = HsRecField' (FieldOcc id) arg
data HsRecField' id arg = HsRecField {
        hsRecFieldLbl :: Located id,
        hsRecFieldArg :: arg,
        hsRecPun      :: Bool }
```


This is used for record construction and pattern-matching. A separate representation is used for updates, namely


```wiki
type HsRecUpdField id = HsRecField' (AmbiguousFieldOcc id) (LHsExpr id)

data AmbiguousFieldOcc name
  = Unambiguous RdrName (PostRn name name)
  | Ambiguous   RdrName (PostTc name name)
```


An `AmbiguousFieldOcc` represents an occurrence of a field that is potentially ambiguous after the renamer, with the ambiguity resolved by the typechecker.  We always store the 'RdrName' that the user originally wrote, and store the selector function after the renamer (for unambiguous occurrences) or the typechecker (for ambiguous occurrences).


### Data families



Consider the following:


```wiki
data family F (a :: *) :: *
data instance F Int  = MkF1 { foo :: Int }
data instance F Bool = MkF2 { foo :: Bool }
```


This is perfectly sensible, and gives rise to two \*different\* record selectors `foo`. Thus we use the name of the first data constructor, rather than the type constructor, when naming the record selectors: we get `$sel:foo:R:MkF1` and `$sel:foo:R:MkF2`. Lexically (in the `GlobalRdrEnv`) the selectors still have the family tycon are their parent.


### Mangling selector names



We could mangle selector names (using `$sel:foo:MkT` instead of `foo`) even when the extension is disabled, but we decided not to because the selectors really should be in scope with their original names, and doing otherwise leads to:


- Trouble with import/export
- Trouble with deriving instances in GHC.Generics (makes up un-renamed syntax using field `RdrName`s)
- Boot files that export record selectors not working


Note that Template Haskell will see the mangled selector names instead of the original field labels, when looking at a datatype declared in a module with `DuplicateRecordFields` enabled. This isn't ideal - should we change the TH representation type so it can get access to both the label and selector?



In the new design, we could perhaps consider only mangling selector names when there is actually a name conflict, but this has not been investigated in any detail.


### Unused imports



Unused imports and generation of the minimal import list (`RnNames.warnUnusedImportDecls`) use a map from selector names to labels, in order to print fields correctly. Moreover, consider the following:


```wiki
module A where
  data T = MkT { x,y:Int }

module B where
  data S = MkS { x,y::Bool }

module C where
  import A( T(x) )
  import B( S(x) )

  foo :: T -> T
  foo r = r { x = 3 }
```


Now, do we expect to report the `import B( S(x) )` as unused? Only the typechecker will eventually know that. Thus field occurrences that are disambiguated during typechecking must be added to `tcg_used_gres`. This set is used to calculate the import usage.


### Template Haskell



At the moment, Template Haskell has no special support for distinguishing between field labels and selector functions, so the TH representation of a datatype compiled with `DuplicateRecordFields` includes selector function names like `$sel:x:MkT`.



Should we modify the TH AST to be able to represent fields correctly? This has been raised as [\#11103](https://gitlab.staging.haskell.org/ghc/ghc/issues/11103).



Similarly, GHC.Generics currently shows the selector name in the metadata, whereas it ought to show the label. But in this case the fix is easy (see [
Phab:D1486](https://phabricator.haskell.org/D1486)).


### Fixity and deprecation



Fixity declarations, and `WARNING` and `DEPRECATED` pragmas, look for a top-level `OccName`.  Thus they apply to all record fields with that label in a single module. The renamer must resolve fixity, so infix duplicate record fields cannot be disambiguated by the typechecker.


### GHC API changes


- The `minf_exports` field of `ModuleInfo` is now of type `[AvailInfo]` rather than `NameSet`, as this provides accurate export information. An extra function `modInfoExportsWithSelectors` gives a list of the exported names including overloaded record selectors (whereas `modInfoExports` includes only non-mangled selectors).
- The `HsExpr`, `hsRecField` and `ConDeclField` AST types have changed as described above.
- TODO are there any more changes? Are the ones we have desirable?

## Outstanding issues


- Haddock compiles, but probably needs updates to document modules with [DuplicateRecordFields](records/overloaded-record-fields/duplicate-record-fields) correctly.
