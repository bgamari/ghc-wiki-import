# Left-Associative `Semigroup` Operator Alias





## Problem



With the implementation of [
prime:Libraries/Proposals/SemigroupMonoid](https://prime.haskell.org/intertrac/Libraries/Proposals/SemigroupMonoid), `Semigroup` will become a superclass of `Monoid`, and consequently `Semigroup((<>))` will be re-exported alongside `Monoid` from the `Prelude` module.


```
-- reduced/simplified definition
class Semigroup a where
    (<>) :: a -> a -> a

infixr 6 <>
```


The `infixr 6`-fixity for `<>` was already introduced 4 years ago, when we added `Data.Monoid.<>` as alias for `mappend` (which differs from `infixr 5 ++`). See also [\#3339](https://gitlab.staging.haskell.org/ghc/ghc/issues/3339) for some of the discussion that began in 2009 leading up to the final `infixr 6 <>` decision.


### Conflicting fixities of `<>` in pretty printing APIs



However, there are a few popular pretty-printing modules which already define a `<>` top-level binding for their respective semigroup/monoid binary operation. The problem now is that those `<>` definitions use a different operator fixity/associativity:


```
-- pretty
module Text.PrettyPrint.Annotated.HughesPJ where

infixl 6 <>
infixl 6 <+>
infixl 5 $$, $+$

-- pretty
module Text.PrettyPrint.HughesPJ where

infixl 6 <>
infixl 6 <+>
infixl 5 $$, $+$
```

```
-- template-haskell
module Language.Haskell.TH.PprLib where

infixl 6 <> 
infixl 6 <+>
infixl 5 $$, $+$
```

```
-- ghc
module Outputable
infixl 9 <> 
infixl 9 <+>
infixl 9 $$, $+$

-- ghc
module Pretty where

infixl 6 <>
infixl 6 <+>
infixl 5 $$, $+$
```


On the other hand, the popular [
hackage:ansi-wl-pprint](http://hackage.haskell.org/package/ansi-wl-pprint) package does use right-associative operators:


```
module Text.PrettyPrint.ANSI.Leijen where

infixr 6 <>
infixr 6 <+>
```


Other pretty printers also using a `infixr 6 <>, <+>` definition:


- [
  hackage:annotated-wl-pprint](http://hackage.haskell.org/package/annotated-wl-pprint)
- [ hackage:mainland-pretty](http://hackage.haskell.org/package/mainland-pretty)

### Changing `<>`'s associativity in pretty-printing APIs



Changing the fixity of `pretty`'s `<>` would however results in a semantic change for code which relies on the relative fixity between `<+>` and `<>` as was [
pointed out by Duncan back in 2011](https://mail.haskell.org/pipermail/libraries/2011-November/017066.html) already:


>
>
> So I was preparing to commit this change in base and validating ghc when I discovered a more subtle issue in the pretty package:
>
>
>
> Consider
>
>
> ```
> a <> empty <+> b
> ```
>
>
> The fixity of `<>` and `<+>` is critical:
>
>
> ```
>   (a <> empty) <+> b
> = {- empty is unit of <> -}
>   (a         ) <+> b
>
>   a <> (empty <+> b)
> = {- empty is unit of <+> -}
>   a <> (          b)
> ```
>
>
> Currently Text.Pretty declares `infixl 5 <>, <+>`. If we change them to be `infixr` then we get the latter  meaning of `a <> empty <+> b`. Existing code relies on the former meaning and produces different output with the latter (e.g. ghc producing error messages like "instancefor" when it should have been "instance for").
>
>

### Unsatisfying Situation Seeking a Long-term Solution



Consequently, it's confusing and bad practice to have a soon-to-be-in-Prelude `<>` operator whose meaning depends on which `import`s are currently in scope. Moreover, a module needs to combine pretty-printing monoids and other non-pretty-printing monoids, the conflicting `<>`s operator needs to be disambiguated via module qualification or similiar techniques.



However, there also seems to be a legitimate use-case for a left-associative `<>` operator.


## Proposed Solution



Add a standardised alias for `<>` to the `Data.Semigroup` vocabulary, e.g.


```
module Data.Semigroup where

infixl 6 ><

-- | Left-associative alias for (right-associative) 'Semigroup' operation '(<>)'
(><) :: Semigroup a => a -> a -> a
(><) = (<>)

```

#### Bikesheds for `><`


- `.<>`
- `<~>`