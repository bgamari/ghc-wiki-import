CONVERSION ERROR

Original source:

```trac
The next version (7.10) of GHC is slated to have a drastically changed Prelude,
driven by the (aptly) named Burning Bridges Proposal (BBP), ticket [ticket:9586]. This proposal generalises
many list operations, e.g. foldr, so they are overloaded - typically becoming members of
either Foldable or Traversable.  A complete list pf changes is given at the foot of this page.  Gershom has made [https://wiki.haskell.org/Foldable_Traversable_In_Prelude a wiki page that describes the change in more detail].

This message comes very late in the release process, but we would urge caution before changing.
Even though the work has been going on for a while, it seems that this
change is coming as a surprise to many people (including Simon Peyton
Jones).

There is much to welcome in BBP, but changing the Prelude cannot be
done lightly since it really is like changing the language.
So I think it's really important for a large number of people to be able to
try out such changes before they come into effect, and to have time
to let the changes stabilize (you rarely get it right the first time).

I've discussed this with a number of people, including Simon PJ, and
we have concrete proposals.

Nothing here proposes any change to AMP (which made `Applicative` a superclass of `Monad`).  AMP is a change to Prelude, but it was discussed for much longer; was brought in over more than one GHC version (GHC 7.8 had custom warnings to encourage library authors to make changes); and is structurally baked into GHC (because of the connection with desugaring).   In contrast BBP is a library-only change.   Anyway, there is no suggestion here to row back on AMP.

== Plan B ==

Simon Marlow rightly points out that adding a new `LANGUAGE` pragma is full of backwards compatibility problems.  So instead of the proposals below, here's a different plan how to introduce BBP.

 1. Leave `Data.List` type signatures and exports alone.
 1. Make ghc 7.10 warn when `Data.List` has been imported in a way that will break with BBP.
 1. No changes to `Prelude` signatures, but ship ghc 7.10 with `PreludeBBP`.
 1. Make `PreludeBBP` the default in ghc 7.12 (or later).

This will give us time to discuss exactly what should go into `Foldable`.

The signature changes in `Control.Monad` and other modules are unrelated to BBP and should be in ghc 7.10.

The ghc warning for importing `Data.List` should be as helpful as possible, something like:
{{{
Foo:5:1  Warning: Data.List imported unqualified, this will conflict with BBP.
         Instead use 'import Data.List(maximumBy, sortBy)'
}}}

=== Possible ghc 7.12 extension ===
If we feel forcing people to change `Data.List` imports is too onerous, I suggest a small extension to name lookup in ghc.  You can declare a symbol to be weak, and in the presence of a weak and a strong symbol the lookup will pick the strong one.  So in `Data.List` you'd say
{{{
  {-# WEAK foldr #-}
  foldr :: (a->b->b) -> b -> [a] -> b
  foldr = ...
}}}
And the `Foldable` version of `foldr` would be normal.  The compiler would then pick the `Foldable` symbol and issue a warning.  I view this extension purely as a device to be used to make these kinds of transitions smooth.

== Proposal 1: ==
 
 * Add a new pragma `{-# LANGUAGE Prelude=AlternativePrelude #-}`
      *   This is a new feature, but it is easy and low-risk to implement.
      *   Which Prelude you use really is a language choice; appropriate for a LANGUAGE pragma.
      *   Semantics is name-space only: import Prelude (); import AlternativePrelude
      *   No effect on desugaring or typing of built-in syntax (list comprehensions, do-notation etc).
 * Ship with both old and new prelude.

With these changes, the current and new behaviour are easy to achieve, in the module or in a .cabal file.
The question becomes "what is the default?".

Another alternative to `{-# LANGUAGE Prelude=AlternativePrelude #-}` would be to use `{-# LANGUAGE NoImplicitPrelude #-}` combined with `import AlternativePrelude`.  This requires source-code changes, but has no issues with backward compatibility with earlier versions of GHC.

== Proposal 2: ==

Make the default be the old rather than the new.
      *   Altering the default Prelude API should be done slowly, with lots of warning; because all users get it willy-nilly.
      *   Unlike AMP, the change is controversial (clearly).
      *   Easier to make changes to New Prelude if it isn't the default.

== Proposal 3: ==

The only remaining problem is that `Data.List` and `Control.Monad` have been similarly generalised, and while changing the Prelude with a language pragma makes sense, changing those modules doesn't.  This problem only exists if, e.g. `Data.List` has been imported unqualified and without an import list.

We can solve this by leaving `Data.List` and `Control.Monad` unchanged and having ghc warn when they are imported in a way that will conflict when BPP goes into effect (in a similar way ghc warned about AMP).  It could look like this:
{{{
Foo:5:1  Warning: Data.List imported unqualified, this will conflict with BBP.  Instead use 'import Data.List(maximumBy, sortBy)'
}}}

== Rationale ==
What is the rationale behind proposal 1?  We want to make it easy to switch an entire package over to an alternative Prelude.  This can be done by putting the pragma in the .cabal file.  Using the `NoImplicitPrelude` doesn't work, because that disables importing the Prelude altogether; we just want a different one, but with desugaring still being done in terms of the regular Prelude.

== Questions ==

Discussing the BBP proposal we also came up with a number of technical questions:

=== Q1 ===
An alternative to Foldable would be
{{{
  class Enumerable t where
    toList :: t a -> [a]   -- Implementations should use 'build'
}}}
Is `Foldable` more general (or efficient) than a `Enumerable` class, plus fusion?

Consider a new data type X a.  I write
{{{
  foldX :: (a -> b -> b) -> b -> X a -> b
  foldX = ...lots of code...

  toList :: X a -> [a]  {-# INLINE toList #-}
  toList x = build (\c n. foldX c n x)
}}}

So now `toList` is small and easy to inline.  Every good list consumer of a call to `toList` will turn into a call to `foldX`, which is what we want.

=== Q2 ===
What are the criteria for being in Foldable?
For instance, why are `sum`, `product` in `Foldable`, but not `and`, `or`?

=== Q3 ===

What's the relationship of `Foldable` to `GHC.Exts.IsList`?
Which also has `toList`, `fromList`, and does work with `ByteString`.
*  For example, could we use `IsList` instead of `Foldable`?
    Specifically, `Foldable` does not use its potential power to apply the type constructor `t` to different arguments.  (Unlike `Traversable` which does.)
{{{
  foldr :: IsList l => (Item l->b->b) -> b -> l -> b
}}}

=== Q4 ===

The operations themselves (listed below) seem to miss some operations that could be generalised (isPrefixOf, scanl, findIndex), and some contexts still use Monad when they could be adjusted to Applicative (sequence_). We suspect there will be additional generalisations in the next version of the base library.

== Some historical remarks ==

Many moons ago a similar generalization of the Prelude was considered.  That time it was about, e.g., generalizing the type of `map` that was generalized to have the type that `fmap` has today.  This proposal was consider too radical and was ultimately rejected.

== Links ==
 * http://neilmitchell.blogspot.co.uk/2014/10/how-to-rewrite-prelude.html
 * http://neilmitchell.blogspot.co.uk/2014/10/why-traversablefoldable-should-not-be.html
 * http://www.yesodweb.com/blog/2014/10/classy-base-prelude, http://www.reddit.com/r/haskell/comments/2if0fu/on_concerns_about_haskells_prelude_favoring/

== Raw diff of changes to base from 7.8 to 7.10 ==
{{{
Control.Monad
+ (<$!>) :: Monad m => (a -> b) -> m a -> m b
- class Monad m
+ class Applicative m => Monad m
- class Monad m => MonadPlus m
+ class (Alternative m, Monad m) => MonadPlus m
- foldM :: Monad m => (a -> b -> m a) -> a -> [b] -> m a
+ foldM :: (Foldable t, Monad m) => (b -> a -> m b) -> b -> t a -> m b
- foldM_ :: Monad m => (a -> b -> m a) -> a -> [b] -> m ()
+ foldM_ :: (Foldable t, Monad m) => (b -> a -> m b) -> b -> t a -> m ()
- forM :: Monad m => [a] -> (a -> m b) -> m [b]
+ forM :: (Traversable t, Monad m) => t a -> (a -> m b) -> m (t b)
- forM_ :: Monad m => [a] -> (a -> m b) -> m ()
+ forM_ :: (Foldable t, Monad m) => t a -> (a -> m b) -> m ()
- guard :: MonadPlus m => Bool -> m ()
+ guard :: (Alternative f) => Bool -> f ()
- mapM :: Monad m => (a -> m b) -> [a] -> m [b]
+ mapM :: (Traversable t, Monad m) => (a -> m b) -> t a -> m (t b)
- mapM_ :: Monad m => (a -> m b) -> [a] -> m ()
+ mapM_ :: (Foldable t, Monad m) => (a -> m b) -> t a -> m ()
- msum :: MonadPlus m => [m a] -> m a
+ msum :: (Foldable t, MonadPlus m) => t (m a) -> m a
- sequence :: Monad m => [m a] -> m [a]
+ sequence :: (Traversable t, Monad m) => t (m a) -> m (t a)
- sequence_ :: Monad m => [m a] -> m ()
+ sequence_ :: (Foldable t, Monad m) => t (m a) -> m ()
- unless :: Monad m => Bool -> m () -> m ()
+ unless :: (Applicative f) => Bool -> f () -> f ()
- when :: Monad m => Bool -> m () -> m ()
+ when :: (Applicative f) => Bool -> f () -> f ()

Data.Bits
+ countLeadingZeros :: FiniteBits b => b -> Int
+ countTrailingZeros :: FiniteBits b => b -> Int
+ toIntegralSized :: (Integral a, Integral b, Bits a, Bits b) => a -> Maybe b

Data.Monoid
+ Alt :: f a -> Alt f a
+ getAlt :: Alt f a -> f a
+ newtype Alt f a

Data.List
- all :: (a -> Bool) -> [a] -> Bool
+ all :: Foldable t => (a -> Bool) -> t a -> Bool
- and :: [Bool] -> Bool
+ and :: Foldable t => t Bool -> Bool
- any :: (a -> Bool) -> [a] -> Bool
+ any :: Foldable t => (a -> Bool) -> t a -> Bool
- concat :: [[a]] -> [a]
+ concat :: Foldable t => t [a] -> [a]
- concatMap :: (a -> [b]) -> [a] -> [b]
+ concatMap :: Foldable t => (a -> [b]) -> t a -> [b]
- elem :: Eq a => a -> [a] -> Bool
+ elem :: (Foldable t, Eq a) => a -> t a -> Bool
- find :: (a -> Bool) -> [a] -> Maybe a
+ find :: Foldable t => (a -> Bool) -> t a -> Maybe a
- foldl :: (b -> a -> b) -> b -> [a] -> b
+ foldl :: Foldable t => (b -> a -> b) -> b -> t a -> b
- foldl' :: (b -> a -> b) -> b -> [a] -> b
+ foldl' :: Foldable t => (b -> a -> b) -> b -> t a -> b
- foldl1 :: (a -> a -> a) -> [a] -> a
+ foldl1 :: Foldable t => (a -> a -> a) -> t a -> a
- foldr :: (a -> b -> b) -> b -> [a] -> b
+ foldr :: Foldable t => (a -> b -> b) -> b -> t a -> b
- foldr1 :: (a -> a -> a) -> [a] -> a
+ foldr1 :: Foldable t => (a -> a -> a) -> t a -> a
+ isSubsequenceOf :: (Eq a) => [a] -> [a] -> Bool
- length :: [a] -> Int
+ length :: Foldable t => t a -> Int
- mapAccumL :: (acc -> x -> (acc, y)) -> acc -> [x] -> (acc, [y])
+ mapAccumL :: Traversable t => (a -> b -> (a, c)) -> a -> t b -> (a, t c)
- mapAccumR :: (acc -> x -> (acc, y)) -> acc -> [x] -> (acc, [y])
+ mapAccumR :: Traversable t => (a -> b -> (a, c)) -> a -> t b -> (a, t c)
- maximum :: Ord a => [a] -> a
+ maximum :: (Foldable t, Ord a) => t a -> a
- maximumBy :: (a -> a -> Ordering) -> [a] -> a
+ maximumBy :: Foldable t => (a -> a -> Ordering) -> t a -> a
- minimum :: Ord a => [a] -> a
+ minimum :: (Foldable t, Ord a) => t a -> a
- minimumBy :: (a -> a -> Ordering) -> [a] -> a
+ minimumBy :: Foldable t => (a -> a -> Ordering) -> t a -> a
- notElem :: Eq a => a -> [a] -> Bool
+ notElem :: (Foldable t, Eq a) => a -> t a -> Bool
- null :: [a] -> Bool
+ null :: Foldable t => t a -> Bool
- or :: [Bool] -> Bool
+ or :: Foldable t => t Bool -> Bool
- product :: Num a => [a] -> a
+ product :: (Foldable t, Num a) => t a -> a
+ scanl' :: (b -> a -> b) -> b -> [a] -> [b]
+ sortOn :: Ord b => (a -> b) -> [a] -> [a]
- sum :: Num a => [a] -> a
+ sum :: (Foldable t, Num a) => t a -> a
+ uncons :: [a] -> Maybe (a, [a])

Foreign.Marshal.Alloc
+ calloc :: Storable a => IO (Ptr a)
+ callocBytes :: Int -> IO (Ptr a)

Foreign.Marshal.Utils
+ fillBytes :: Ptr a -> Word8 -> Int -> IO ()

Foreign.Marshal.Array
+ callocArray :: Storable a => Int -> IO (Ptr a)
+ callocArray0 :: Storable a => Int -> IO (Ptr a)

Control.Exception.Base
+ AllocationLimitExceeded :: AllocationLimitExceeded
+ data AllocationLimitExceeded
+ displayException :: Exception e => e -> String

Control.Exception
+ AllocationLimitExceeded :: AllocationLimitExceeded
+ data AllocationLimitExceeded
+ displayException :: Exception e => e -> String

Prelude
+ (*>) :: Applicative f => f a -> f b -> f b
+ (<*) :: Applicative f => f a -> f b -> f a
+ (<*>) :: Applicative f => f (a -> b) -> f a -> f b
- all :: (a -> Bool) -> [a] -> Bool
+ all :: Foldable t => (a -> Bool) -> t a -> Bool
- and :: [Bool] -> Bool
+ and :: Foldable t => t Bool -> Bool
- any :: (a -> Bool) -> [a] -> Bool
+ any :: Foldable t => (a -> Bool) -> t a -> Bool
- class Monad m
+ class Applicative m => Monad m
+ class Monoid a
+ class Functor f => Applicative f
+ class Foldable t
+ class (Functor t, Foldable t) => Traversable t
- concat :: [[a]] -> [a]
+ concat :: Foldable t => t [a] -> [a]
- concatMap :: (a -> [b]) -> [a] -> [b]
+ concatMap :: Foldable t => (a -> [b]) -> t a -> [b]
+ data Word :: *
- elem :: Eq a => a -> [a] -> Bool
+ elem :: (Foldable t, Eq a) => a -> t a -> Bool
+ foldMap :: (Foldable t, Monoid m) => (a -> m) -> t a -> m
- foldl :: (b -> a -> b) -> b -> [a] -> b
+ foldl :: Foldable t => (b -> a -> b) -> b -> t a -> b
- foldl1 :: (a -> a -> a) -> [a] -> a
+ foldl1 :: Foldable t => (a -> a -> a) -> t a -> a
- foldr :: (a -> b -> b) -> b -> [a] -> b
+ foldr :: Foldable t => (a -> b -> b) -> b -> t a -> b
- foldr1 :: (a -> a -> a) -> [a] -> a
+ foldr1 :: Foldable t => (a -> a -> a) -> t a -> a
- length :: [a] -> Int
+ length :: Foldable t => t a -> Int
- mapM :: Monad m => (a -> m b) -> [a] -> m [b]
+ mapM :: (Traversable t, Monad m) => (a -> m b) -> t a -> m (t b)
- mapM_ :: Monad m => (a -> m b) -> [a] -> m ()
+ mapM_ :: (Foldable t, Monad m) => (a -> m b) -> t a -> m ()
+ mappend :: Monoid a => a -> a -> a
- maximum :: Ord a => [a] -> a
+ maximum :: (Foldable t, Ord a) => t a -> a
+ mconcat :: Monoid a => [a] -> a
+ mempty :: Monoid a => a
- minimum :: Ord a => [a] -> a
+ minimum :: (Foldable t, Ord a) => t a -> a
- notElem :: Eq a => a -> [a] -> Bool
+ notElem :: (Foldable t, Eq a) => a -> t a -> Bool
- null :: [a] -> Bool
+ null :: Foldable t => t a -> Bool
- or :: [Bool] -> Bool
+ or :: Foldable t => t Bool -> Bool
- product :: Num a => [a] -> a
+ product :: (Foldable t, Num a) => t a -> a
+ pure :: Applicative f => a -> f a
- sequence :: Monad m => [m a] -> m [a]
+ sequence :: (Traversable t, Monad m) => t (m a) -> m (t a)
+ sequenceA :: (Traversable t, Applicative f) => t (f a) -> f (t a)
- sequence_ :: Monad m => [m a] -> m ()
+ sequence_ :: (Foldable t, Monad m) => t (m a) -> m ()
- sum :: Num a => [a] -> a
+ sum :: (Foldable t, Num a) => t a -> a
+ traverse :: (Traversable t, Applicative f) => (a -> f b) -> t a -> f (t b)

Data.Function
+ (&) :: a -> (a -> b) -> b

Control.Monad.Instances
- class Monad m
+ class Applicative m => Monad m

Data.Coerce
- coerce :: Coercible k a b => a -> b
+ coerce :: Coercible * a b => a -> b

Data.Version
+ makeVersion :: [Int] -> Version

Data.Complex
- cis :: RealFloat a => a -> Complex a
+ cis :: Floating a => a -> Complex a
- conjugate :: RealFloat a => Complex a -> Complex a
+ conjugate :: Num a => Complex a -> Complex a
- imagPart :: RealFloat a => Complex a -> a
+ imagPart :: Complex a -> a
- mkPolar :: RealFloat a => a -> a -> Complex a
+ mkPolar :: Floating a => a -> a -> Complex a
- realPart :: RealFloat a => Complex a -> a
+ realPart :: Complex a -> a

Data.Foldable
+ length :: Foldable t => t a -> Int
+ null :: Foldable t => t a -> Bool

System.Exit
+ die :: String -> IO a

Numeric.Natural
+ data Natural

Data.Bifunctor
+ bimap :: Bifunctor p => (a -> b) -> (c -> d) -> p a c -> p b d
+ class Bifunctor p
+ first :: Bifunctor p => (a -> b) -> p a c -> p b c
+ second :: Bifunctor p => (b -> c) -> p a b -> p a c

Data.Functor.Identity
+ Identity :: a -> Identity a
+ newtype Identity a
+ runIdentity :: Identity a -> a

Data.Void
+ absurd :: Void -> a
+ data Void
+ vacuous :: Functor f => f Void -> f a
}}}

```
