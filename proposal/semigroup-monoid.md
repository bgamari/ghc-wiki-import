# Semigroup (as superclass of) Monoid Proposal



THIS DESCRIPTION IS STILL WORK IN PROGRESS



Please comment on [\#10365](https://gitlab.staging.haskell.org/ghc/ghc/issues/10365) if you notice some show-stopper issue



Introducing `Semigroup` as a superclass of `Monoid` has been proposed several times (in reverse chronological order):


- [
  http://thread.gmane.org/gmane.comp.lang.haskell.libraries/24494](http://thread.gmane.org/gmane.comp.lang.haskell.libraries/24494)
- [
  http://thread.gmane.org/gmane.comp.lang.haskell.libraries/19649](http://thread.gmane.org/gmane.comp.lang.haskell.libraries/19649)
- TODO ...

## Final API



The final API (suitable for Haskell Report inclusion) we want to end up with is


```
module Data.Semigroup where

class Semigroup a where
    (<>) :: a -> a -> a

    sconcat :: NonEmpty a -> a
    sconcat (a :| as) = go a as
      where
        go b (c:cs) = b <> go c cs
        go b []     = b

    -- GHC extension, not needed for Haskell Report
    stimes :: Integral b => b -> a -> a
    stimes y0 x0 = {- default impl -}
```

```
module Data.Monoid where

class Semigroup a => Monoid a where
    mempty  :: a

    mconcat :: [a] -> a
    mconcat = foldr (<>) mempty

    -- GHC extension, not needed for Haskell Report
    mtimes :: Integral b => b -> a -> a
    mtimes y0 x0 = {- default impl -}

-- GHC Extension: Legacy alias not needed for Haskell Report
mappend :: Semigroup a => a -> a -> a
mappend = (<>)
```

## Migration plan


### Phase 1 (GHC 8.0)



Move `Data.Semigroup` & `Data.List.NonEmpty` from `semigroups-0.18` to `base`.



(maybe) Implement a warning about definitions of an operator named (\<\>) that indicate it will be coming into Prelude in 8.2. We should warn about missing Semigroup instances at any use site of (\<\>) as they'll break in 8.2.


### Phase 2 (GHC 8.2)



TODO ...integrate migration roadmap outlined in [
http://permalink.gmane.org/gmane.comp.lang.haskell.libraries/24526](http://permalink.gmane.org/gmane.comp.lang.haskell.libraries/24526)

