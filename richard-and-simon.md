# Summary of tasks to be completed



... as discussed by Richard and Simon. This page is mostly for our own notes, but others are welcome to read it.


- Implement homogeneous as per Stephanie's paper
- Fix [\#11715](https://gitlab.staging.haskell.org/ghc/ghc/issues/11715) according to Richard's plan
- Change flattener to be homogeneous ([\#12919](https://gitlab.staging.haskell.org/ghc/ghc/issues/12919))
- Remove `solveSomeEqualities`
- Generalized injectivity [\#10832](https://gitlab.staging.haskell.org/ghc/ghc/issues/10832), vis-a-vis Constrained Type Families paper
- [\#13333](https://gitlab.staging.haskell.org/ghc/ghc/issues/13333) (Typeable regression)
- Taking better advantage of levity polymorphism:

  - Could `[]` be a data family?
  - Unlifted newtypes
  - Unlifted datatypes
  - generalized classes in base
  - ...
- [\#11739](https://gitlab.staging.haskell.org/ghc/ghc/issues/11739) (simplify axioms)
- Fix all the `TypeInType` bugs


**Iceland\_jack**: By `[]` as a data family do you mean:


```
data family [] (a :: TYPE (rep :: RuntimeRep)) :: Type

data instance [] (a :: Type)        = []   | a : [a]
data instance [] (a :: TYPE IntRep) = INil | ICons a [a]
...
```


I invite you to look at [
this gist](https://gist.github.com/Icelandjack/1824f4544c86b4ab497282783f94c360) posted on [\#12369](https://gitlab.staging.haskell.org/ghc/ghc/issues/12369) and [\#13341](https://gitlab.staging.haskell.org/ghc/ghc/issues/13341).

