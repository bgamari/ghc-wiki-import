## No Record Selector Functions



This proposal is a precursor to overloaded record fields. It's also a modest step towards freeing up the namespace, without in any way pre-judging how the 'narrow namespace issue' might get addressed. Ticket [\#5972](https://gitlab.staging.haskell.org/ghc/ghc/issues/5972).



There is to be a compiler flag **-XNoRecordSelectorFunctions**. (Default value **‑XRecordSelectorFunctions**, to give H98 behaviour.)



`-XNoRecordSelectorFunctions` suppresses creating the field selector function from the field name in a record-style data declaration.



Suppressing the function frees up the namespace, to be able to experiment with various record/field approaches -- including the 'cottage industry' of Template Haskell solutions.



In particular, this means we can declare more than one record type within a module using the same field name.



`-XNoRecordSelectorFunctions` implies `-XDisambiguateRecordFields` -- otherwise the only way to access record fields would be positionally. It also implies `‑XNamedFieldPuns` and `‑XRecordWildCards` to support field access and update. (IMHO, suppressing the field selector function should always have been part of `-XDisambiguateRecordFields`. I'm by no means the first to make that observation.) 


- Note that the field name is still valid within the scope of a pattern match, or record update inside the `MkT{...}` explicit constructor syntax.
- But record update won't work, because the field name alone doesn't uniquely identify the record type.
  (That is, the syntax with a record or expression prefix to the braces `e{ x = True }` -- there might be multiple record types declared in the module with field name x.)


Example use case: [
http://www.haskell.org/pipermail/haskell-cafe/2009-May/061879.html](http://www.haskell.org/pipermail/haskell-cafe/2009-May/061879.html) (referred from an old Wiki discussion on TDNR [
http://www.haskell.org/haskellwiki/TypeDirectedNameResolution](http://www.haskell.org/haskellwiki/TypeDirectedNameResolution) .)



See also thread starting: [
http://www.haskell.org/pipermail/glasgow-haskell-users/2012-January/021433.html](http://www.haskell.org/pipermail/glasgow-haskell-users/2012-January/021433.html) (and continuing through February), which initially considers nested modules as an approach for namespacing.


### Import/Export and Representation hiding



Since there is no field selector function created, it can't be exported or imported.



If you say:


```wiki
{-# OPTIONS_GHC -XNoRecordSelectorFunctions              #-}
module M( T( x ) )       where
    data T = MkT { x, y :: Int }
```


then the existence of field `y` is hidden;
type `T` and field label `x` are exported, but not data constructor `MkT`, so `x` is unusable.



(Without the `‑XNoRecordSelectorFunctions` flag, field selector function `x` would be exported.)



There's an effect on **importing** modules even if they don't set `-XNoRecord...`, so that they are generating selector functions for record types they declare:


- The record types imported do not have field selector functions, so compilation must not generate code to (try to) use them.
- But other uses of the field name must still work: (pattern matching, punning and wildcards, record creation using explicit data constructor (and `-XDisambiguateRecordFields`).
- Record update `e{ x = True }` won't work.
