# Closed type families with overlapping equations



This page describes an extension to type families that supports overlap.


- See also the ** [Discussion Page](new-axioms/discussion-page) ** added May 2012 (and now rather out-of-date), for comment/suggestions/requests for clarification/alternative solutions, to explore the design space.
- See also the ** [Coincident Overlap](new-axioms/coincident-overlap) ** page (added August 2012) for a discussion around the usefulness of allowing certain overlaps when the right-hand sides coincide.
- See also the ** [Template Haskell](new-axioms/template-haskell) ** page (added December 2012) for a proposal for the Template Haskell changes necessary to support this change.
- See also the ** [Non-linearity](new-axioms/nonlinearity) ** and ** [Closed Type Families](new-axioms/closed-type-families) ** pages (added May 2013) for discussion and a proposal around type unsoundness that can be caused by repeated variables on the left-hand side of an instance. The proposal on that page will likely be implemented and will then be copied here.


See also 


- [\#8154](https://gitlab.staging.haskell.org/ghc/ghc/issues/8154)
- [\#8155](https://gitlab.staging.haskell.org/ghc/ghc/issues/8155)
- [
  Email thread](http://www.haskell.org/pipermail/glasgow-haskell-users/2013-August/022712.html) on overlap restrictions for open families
- [
  Draft paper](http://www.cis.upenn.edu/~eir/papers/2014/axioms/axioms-extended.pdf) about closed type families


Status: A working implementation with closed type families has been pushed to HEAD. The description of the feature below is accurate as of Jun 24, 2013.


## Background



One might imagine that it would be a simple matter to have a type-level function


```wiki
type family Equal a b :: Bool
```


so that `(Equal t1 t2)` was `True` if `t1`=`t2` and `False` otherwise.  But it isn't.  



You can't write


```wiki
type instance Equal a a = True
type instance Equal a b = False
```


because System FC (rightly) prohibits overlapping family instances.  



Expanding this out, you can do it for a fixed collection of types thus:


```wiki
type instance Equal Int Int = True
type instance Equal Bool Bool = True
type instance Equal Int Bool = False
type instance Equal Bool Int = False
```


but this obviously gets stupid as you add more types.  



Furthermore, this is not what you want. Even if we restrict the equality function to booleans


```wiki
type family Equal (a :: Bool) (b :: Bool) :: Bool
```


we can't define instances of Equal so that a constraint like this one


```wiki
Equal a a ~ True
```


is satisfiable---the type instances only reduce if a is known to True or False. GHC doesn't reason by cases.  (Nor should it, \|Any\| also inhabits \|Bool\|. No kinds really are closed.)



The only way to work with this sort of reasoning is to use Overlapping Instances, as suggested in the [
HList paper.](http://homepages.cwi.nl/~ralf/HList/)


## What to do about it



A new version of axioms is now implemented. The formal treatment can be found in docs/core-spec/core-spec.pdf.



Here are the changes to source Haskell.


-  A `type family` declaration can now define equations, making a closed type family:

  ```wiki
  type family Equal a b :: Bool where
    Equal a a = True
    Equal a b = False
  ```

- Patterns within a single closed declaration may overlap, and are matched top to bottom.

- Open type families are unchanged, except that a certain corner case of instances with a non-trivial overlap is disallowed. See [here](new-axioms/nonlinearity). I (Richard) do not expect this change to break much code.

- A closed family does not need to be exhaustive. If there is no equation that matches, the call is stuck. (This is exactly as at present.)

- An error is issued when a later equation is matched by a former, making the later one inaccessible.

  ```wiki
  type family F a where
    F (a,b)   = [Char]
    F (Int,b) = Char
  ```

  Here the second equation can never match.

  For closed kinds (and maybe for open ones, but I can't unravel it), it seems possible to write a set of equations that will catch all possible cases but doesn't match the general case. This situation is currently (Dec 2012) undetected, because I (Richard, `eir` at `cis.upenn.edu`) am unconvinced I have a strong enough handle on the details. For example, what about `Any`?

- The equations do not need to share a common pattern:

  ```wiki
  type family F a where
    F Int   = Char
    F (a,b) = Int
  ```

- When matching a use of a closed type family, special care must be taken (by GHC) not to accidentally introduce incoherence. Consider the following example:

  ```wiki
  type family F a where
    F Int = Bool
    F a   = Char
  ```

  and we try to simplify the type `F b`. The naive implementation would just simplify `F b` to `Char`, but this would be wrong. The problem is that `b` may later be unified with `Int`, meaning `F b` should simplify to `Bool`, not `Char`. So, the correct behavior is not to simplify `F b` at all; it is stuck for now. Note that the second equation above is not useless: we will still simplify, say, `F Double` to `Char`.

- More formally, we only match a type (called the target) against an equation in a closed type family when, for all previous equations: the LHS is *apart* from the target, **or** the equation is *compatible* with the chosen one. Apartness and compatibility are defined below.

- Two types are *apart* when they cannot simplify to a common reduct, even after arbitrary substitutions. This is undecidable, in general, so we implement a conservative check as follows: two types are considered to be apart when they cannot unify, omitting the occurs check in the unification algorithm.

- Two equations are *compatible* if, either, their LHSs are apart, or their RHSs are syntactically equal after substitution with the unifier of their LHSs. This last condition allows closed type families to participate in the coincident overlap that open families have supported for years.


For example:


```wiki
type family And (a :: Bool) (b :: Bool) where
  And False a     = False
  And True  b     = b
  And c     False = False
  And d     True  = d
  And e     e     = e
```


All of these equations are compatible, meaning that GHC does not have to do the apartness check against the target.


## Limitations



The implementation described above does not address all desired use cases. In particular, it does not work with associated types at all. The biggest reason not to add associated types into the mix is that it will be a confusing feature. Overlap among class instances is directed by specificity; overlap among family instances is ordered by the programmer. Users would likely expect the two to coincide, but they don't and can't, as it would not be type safe:



It seems that inter-module overlapping non-coincident associated types are a Bad Idea, but please add comments if you think otherwise and/or need such a feature. Why is it a Bad Idea? Because it would violate type safety: different modules with different visible instances could simplify type family applications to different ground types, perhaps concluding `True ~ False`, and the world would immediately cease to exist.



This last point doesn't apply to overlapping type class instances because type class instance selection compiles to a term-level thing (a dictionary). Using two different dictionaries for the same constraint in different places may be silly, but it won't end the world.


