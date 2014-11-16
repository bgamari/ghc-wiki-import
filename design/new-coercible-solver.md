CONVERSION ERROR

Original source:

```trac
(In the text below, "I" = Richard Eisenberg)

== Proposed change ==

In the work on my Dependent Haskell branch, I need to think about representational kind equalities (explanation below). The problem is that nominal equality and representational equality are treated very, very differently in the solver. It seems unifying the two treatments, as much as possible, would be good. Concretely, I propose a new constructor for `PredTree`, `EqReprPred`, that would be quite like `EqPred` but for representational equality. (It's possible that we should just add a new `Role` field to `EqPred`, but my hunch is that representational equality and nominal equality are still different enough that a new top-level constructor will be easier to work with.) We would then have functions like `canEqRepr` in !TcCanonical to canonicalize representational equality predicates, and corresponding changes in !TcInteract.

== Benefits ==

There are several benefits of this change:
- I think a more similar treatment of representational equality and nominal equality would be easier to think about. It happens to be true that `Coercible` is a class, but looking back now, that fact seems like more historical accident than the Right Design (though I most certainly agreed with making `Coercible` a class at the time). After all, `Coercible`'s nearest cousin is `(~)`, which is resolutely ''not'' a class.

- A custom canonicalizer for representational equality would allow code like

{{{#!hs
import Data.Type.Coercion
import Data.Coerce

foo :: Coercible (Maybe a) (Maybe b) => Coercion a b
foo = Coercion
}}}

  That module is currently rejected, because GHC can't unpack the `Coercible a b` from the given.

- To my shock and horror, if I want kind equalities, I need the solver to think about both lifted and unlifted nominal equality (I knew that) and also (very sadly) lifted and unlifted representational equality. The current setup has just enough room for me to somewhat-ungracefully shoehorn unlifted nominal equality into the solver, but there's just no way to squeeze in unlifted representational equality without drastic changes.

- This change might address #9117 more fully.

== Drawbacks ==

This change would add more complexity to something already complex.

== Explanation of representational kind equalities in Dependent Haskell ==

(This section can safely be skipped.)

When we kind-cast a type like `t |> g`, the `g` must be representational. This is to have uniformity with the term level, and therefore the ability to promote more expressions to types. For the system to stay glued together, we also must be able to prove `t ~N (t |> g)` -- in other words, that coercions are irrelevant, even in nominal equality.

The enhanced FC has a coercion former `kind` that extract a kind equality out of a heterogeneous type equality. Suppose `t :: k1)`, `g :: k1 ~R k2`, and `h :: t ~N (t |> g)`. Then, `kind h :: k1 ~? k2`. It would be disastrous for `kind h` to have a nominal role, because we've effectively then promoted `g`'s representational role to be nominal. Thus, it is necessary for `kind h` always to be representational.

Now, suppose we have `t :: k1`, `s :: k2`, and `[G] g :: t ~N s`. I've retained the invariant that canonical equalities are homogeneous, so that they can be used in substitution. (An alternate design would be to let them be heterogeneous but then add a homogenizing coercion during substitution, but that seems strictly worse.) 
During canonicalization, I wish to break apart `g` into two equalities, `[G] h1 :: k1 ~ k2` and `[G] h2 :: t ~ (s |> sym h1)`. What should the roles be? The evidence for `h1` must be `kind g`. Thus, `h1`'s role is representational. Given that `g` has a nominal role, we would want `h2`'s role to be nominal as well. So, we get `[G] h1 :: k1 ~R k2` and `[G] h2 :: t ~N (s |> sym h1)`, and we can continue canonicalizing. And, here is where the problem shows up: we now have `h1`, an unavoidable (to me) representational kind equality.

== Conclusion ==

I'm happy to do this work, and would do the majority of the rewrite in the master branch and then merge into my Dependent Haskell branch.

What do we think of this?
```