
On this page we describe the principles behind the implementation of the linear types extension as described at [LinearTypes](linear-types).



The current implementation can be reviewed on [
Phabricator](https://phabricator.haskell.org/D5401). There is also a [
GitHub repository](https://github.com/tweag/ghc/tree/linear-types) which contains the history of the project and a list of known issues.



Authors or the implementation are:


- Krzysztof Gogolewski
- Matthew Pickering
- Arnaud Spiwack


The implementation is supported by a [
document formalising (a simplified version) of linear core](https://ghc.haskell.org/trac/ghc/attachment/wiki/LinearTypes/Implementation/minicore.2.pdf). This is more complete than the paper. It is work in progress.


## Very high-level summary



The linear types branch adds a new extension `-XLinearTypes`. This allows programs to use the linear arrow `a ->. b` and the multiplicity-polymorphic arrow `a -->.(m) b`, where `m` is any type which belongs to the new Multiplicity kind, with two types `One` and `Omega`.  The linear arrow `⊸` is available when compiling with `-XUnicodeSyntax`. The final syntax is not decided yet and subject to change.



Example:


```wiki
id :: a -->.(One) a   -- or: id :: a ->. a
id x = x
```


is the linear identity function.



The syntax is all temporary.


### funTyCon and unrestrictedFunTyCon



In the GHC, prior to linear types, `funTyCon` is the `(->)` type, of kind `forall {a b :: RuntimeRep}. TYPE a -> TYPE b -> Type`.



In the linear types branch, `funTyCon` refers to a new primitive type called `FUN`, of kind `forall (m :: Multiplicity) -> forall {a b :: RuntimeRep}. TYPE a -> TYPE b -> Type`. The `(->)` arrow is represented by `unrestrictedFunTyCon` and is a type synonym for `FUN 'Omega`. Note that the kind of `(->)` stays the same. `(->)` is moved from `TysPrim` to `TysWiredIn`.



There is no `(->.)` (linear arrow) type constructor yet. It will eventually be defined as a type synonym in `TysWiredIn` like `(->)`.



The additional multiplicity parameter to `funTyCon` is the main principle behind the implementation. The rest of the implementation is essentially correctly propagating and calculating linearity information whenever a `FunTy` is created.



The data type for multiplicities is defined in `compiler/basicTypes/Multiplicity.hs` and is called `Mult`.



In binders, where we stored a type, we now store a pair of type and multiplicity. This is achieved with data type `Scaled` which stores a type together with a multiplicity (it's a convention similar to `Located` for storing source locations).


## Frontend


### Typechecking



The internal representation of a multiplicity is called `Mult`. Its (slightly simplified) definition is as follows:


```
data Mult = One
          | Omega
          | MultAdd Mult Mult
          | MultMul Mult Mult
          | MultThing Type
```


Each constructor represents how many times a variable is allowed to be used.



The checking algorithm is as follows:


- The typechecking monad is enriched with an extra writer-like state called the *usage environment*, which is a mapping from variables to multiplicities. Variables not present in the usage environment are considered to have usage Zero (which is not a multiplicity).
- The usage environment output by `u v` is `U1 + p * U2` where

  - `U1` is the usage environment output by `u`
  - `U2` is the usage environment output by `v`
  - `u :: _ -->.(p) _`
- Adding a new variable `x` with multiplicity `p` to the context to typecheck `u` is performed as

  - `x` is added to the context
  - `u` is typechecked in that context
  - The usage `q` of `x` is retrieved from the output usage environment
  - It is checked that `q <= p`
  - `x` is removed from the output usage environment


Concretely, there are precisely two places where we check how often variables are used.


1. In `TcEnv.tc_extend_local_env`, which is the function which brings local variables into scope. Upon exiting a binder, we call `tcSubMult` (via `check_binder`) to ensure that the variable usage is compatible with the declared multiplicity (if no multiplicity was declared, a fresh existential multiplicity variable is created instead).

  - `tcSubMult` emits constraints of the form `π ⩽ ρ`. In `check_binder` there is a call to `submultMaybe` which checks for obvious cases before we delegate to `tcSubMult` which decomposes multiplication and addition constraints

>
> >
> >
> > before calling `tcEqMult` in order to check for precise equality of types. This function will also do unification of variables.
> >
> >
>

1. In `tc_sub_type_ds`, In the `FunTy` case, we unify the arrow multiplicity which can lead to the unification of multiplicity variables.

  - `tc_sub_type_ds` emits constraints of the form `π = ρ`, this is achieved by a call to `tcEqMult` which just calls `rigToType` on the `Rig`s before checking for equality using `tc_sub_type_ds` recursively.


A better implementation would probably emit a real constraint `pi <= rho` and then add logic for solving it to the constraint solver. The current ad-hoc approach reduces the task of checking the relation to checking certain equality constraints.



In order to use the normal unification machinery, we eventually call `tc_sub_type_ds` but before that we check for domain specific rules we want to implement such as `1 <= p` which is achieved by calls to `submult` or `submultMaybe`.


#### Solving constraints



Constraint solving is not completely designed yet. The current implementation follows very simple rules, to get the implementation off the ground. Basically both equality and subsumption constraints are treated as syntactic equality unification (as opposed, in particular, to unification up to laws of multiplicities as in the proposal). There are few other rules (described below) which are necessary to type even simple linear programs:


##### The 1 \<= p rule



Given the current domain, it is true that `1` is the smallest element. As such, we assume `1` is smaller than everything which allows more functions to type check.



This is implemented by making sure to call `submult` on the multiplicities before passing them to the normal unifier which knows nothing special about multiplicities. This can be seen at both
`tc_extend_local_env` and `tc_sub_type_ds`. At the moment we also get much better error messages by doing this short circuiting.


##### Complex constraints



Multiplication and addition are approximated.


- A constraint of the form `p1 * p2 <= q` is solved as `p1 <= q` and `p2 <= q`.
- A constraint of the form `p1 + p2 <= q` is solved as `Omega <= q`

### Defaulting



Unsolved multiplicity variables are defaulted to ω by the following functions:



Calls to `isMultiplicityVar` are used in places where we do defaulting.


1. `TcSimplify.defaultTyVarTcS`
1. `TcMType.defaultTyVar`

### HsType



We need to distinguish `a -> b`, `a ->. b` and `a -->.(m) b` in the surface syntax. The `HsFunTy` constructor has an extra field containing `HsArrow`, which stores this information:


```
data HsArrow pass
  = HsUnrestrictedArrow
    -- ^ a -> b
  | HsLinearArrow
    -- ^ a ->. b
  | HsExplicitMult (LHsType pass)
    -- ^ a -->.(m) b (very much including `a -->.(Omega) b`! This is how the
    -- programmer wrote it). It is stored as an `HsType` so as to preserve the
    -- syntax as written in the program.
```

### Data Constructors are polymorphic



A key part of the original proposal was the type of data constructors was linear.


```
(,) :: a ->. b ->. (a, b)
```


They were then automatically η-expanded automatically when needed to be used as unrestricted types. That is


```
( (,) :: a -> b -> (a, b))
```


would typecheck because it would be elaborated into `\x y -> (x, y)`, which can be given the type `a -> b -> (a, b)`.



However, besides the fact that over-eager η-expansion is undesirable (it is not perfectly preserving: `undefined `seq` True` loops, while `(\x -> undefined x) `seq` True` terminates, it also runs contrary to the [
plans on polymorphism](https://www.microsoft.com/en-us/research/publication/guarded-impredicative-polymorphism/)), it was found to be also insufficient for backwards compatibility. Below are two examples of perfectly good Haskell code which would start failing with this strategy.


#### Example 1


```
foo :: Identity (a -> b) -> a -> b
foo = unIdentity

foo (Identity Just)
```


The last line doesn’t type check because foo expects an \`Identity (a
-\> b)` but `Identity Just` is inferred to have type `Identity (a ⊸
b)\`. The limited subtyping doesn’t handle this case. In a perfect
world, `Identity Just` would have been elaborated to \`Identity (\\x -\>
Just x) :: Identity (a -\> b)\`, but at the time where the application
`Identity` Just was type-checked, it was not known that Just should
be cast to an unrestricted arrow type.


#### Example 2



The second problem is much more obvious in retrospect: there are
typeclasses defined on (-\>), and they can fail to apply when using
linear function.


```
import Control.Category

Just . Just -- fails
```


The latter fails because there are no `Category` instance of `(⊸)`. In
the case of `Category`, we can write an instance, but it’s not
necessarily the case that an instance for `(->)` will have a
corresponding instance for `(⊸)`. Anyway, even if we have a `Category`
instance for `(⊸)`, it is not clear what expression will typecheck:
probably, one of the following will fail


```
import Control.Category

(Just :: _ -> _) . Just
Just . (Just :: _ -> _)
```

#### Polymorphic Constructors



Simon suggested a solution to these problems. To make the type of linear data constructors polymorphic, when they are used as terms (their type stays linear when they are used in patterns).


```
(,) :: forall (p :: Multiplicity) (q :: Multiplicity). a -->.(p) b  -->.(q) -> (a, b)
```


Currently simplified as having a single multiplicity variable for the sake of a simpler implementation


```
(,) :: forall (p :: Multiplicity). a -->.(p) b -->.(p) -> (a, b)
```


We never infer multiplicity polymorphic arrows (like levity polymorphism). Any type variables which get to the top level are default to `Omega`. Thus, in most cases the multiplicity argument is defaulted to `Omega` or forced to be `Omega` by unification.


##### Implementation



The way this is implemented is that every data constructor is given a wrapper with very few exceptions (sole exceptions: levity polymorphic constructors (*i.e.* unboxed tuple and unboxed sum constructors) because they cannot have wrappers due to the levity-polymorphism restriction, and dictionary constructor because they don't exist in the surface syntax).



Even internally used data types have wrappers for the moment which makes the treatment quite uniform. The most significant challenge of this endeavour was giving built-in data types wrappers as previously none of them had wrappers. This led to the refactoring of `mkDataConRep` and the
introduction of `mkDataConRepSimple` which is a pure, simpler version of `mkDataConRep`.



Once everything has a wrapper, you have to be quite careful with the difference between `dataConWrapId` and `dataConWorkId`. There were a few places where this used to work
by accident as they were the same thing for builtin types.



In particular, functions like `exprConApp_maybe` are fragile and I (Matthew) spent a while looking there.



Other uses can be found by grepping for `omegaDataConTy` (there are very few of these left, as most of them pertained to type-class dictionaries which we reverted to have no wrapper). There are probably places where they can be removed by using `dataConWorkId` rather than `dataConWrapId`. The desugaring of `ConLikeOut` uses `dataConWrapId` though so anything in source syntax should apply the extra argument.



As another note, be warned that the serialisation for inbuilt tuples is different from normal data constructors. I didn't know this until I printed out the uniques - ad397aa99be3aa1f46520953cb195b97e4cfaabf



Otherwise, the implementation followed much the same path as levity polymorphism.



As part of this implementation `dataConUserTy` has been change to reflect the extra multiplicity parameter of data constructors. However, this may have been a mistake. I (aspiwack) am beginning to think that `dataConUserTy` should stay as-the-user-type it, and the type of the wrapper should be a different type. I suspect this choice to be involved in \[a printing issue [
https://github.com/tweag/ghc/issues/243](https://github.com/tweag/ghc/issues/243)\].


#### Rules and Wrappers



Giving data constructors wrappers makes RULES mentioning data constructors not work as well. Mentioning a data constructor in a RULE currently means the wrapper, which is often inlined without hesitation and hence means that rule will not fire at a later point. We solve this by inlining the wrapper late (in phase 0); see [\#15840](https://gitlab.staging.haskell.org/ghc/ghc/issues/15840) and [
https://phabricator.haskell.org/D5400](https://phabricator.haskell.org/D5400).


#### `magicDict`



A specific point of pain was `magicDict` which is a special identifier which does not exist at runtime. It relies on an inbuilt RULE in order to eliminate it.
The rule is defined at `match_magicDict`.



If you don't eliminate it then you will get a linker error like


```wiki
/root/ghc-leap/libraries/base/dist-install/build/libHSbase-4.12.0.0-ghc8.5.so: undefined reference to `ghczmprim_GHCziPrim_magicDict_closure
```


[
I (Matthew) made the matching more robust](https://github.com/tweag/ghc/pull/92/commits/c18ab3d533dbc871f5afe8fe4d2a9d8f8213f8b4) in the two places in base by using a function as the argument to `magicDict` rather than a data constructor
as the builtin rule only uses that information for the type of the function.


### Do-notation/rebindable syntax



Type-checking of the do notation relies on typing templates (`SyntaxOpType`). The type of `(>>=)` (in particular), in the context, is matched against its template, returning the type.



In order to support `(>>=)` operators with varying multiplicities, function templates (`SynFun`) now return their multiplicity. Specifically `SyntaxOpType` now returns a second list of all the multiplicities (from left to right) which it computed.



I (aspiwack) introduced a second list at a time where there wasn't a correspondence between types and multiplicities. It could be changed to return multiplicities as types in the main list. It's not much of a simplification, though.


## Core


### Core Lint



Core variables are changed to carry a multiplicity. The multiplicity annotation on the case, as in the paper, is the multiplicity of the case-binder.



In Core, non-recursive lets are changed compared to the paper (see Core-to-core passes below): instead of carrying a multiplicity, their multiplicity is type-checked as is they were inline at the usage point. To represent this, variables, in actuality, either carry a multiplicity (`Regular`) or they are what we've been calling in the code, an alias-like variable (`Alias`). (recursive let binders always have multiplicity `Omega`)



The linter is modified in two ways to account for linearity. First the main loop (`lintCoreExpr`) returns a usage environment in addition to a type. This is like the surface-language type checker. In addition, the environment in the `LintM` monad is modified to carry a mapping from alias-like variables to the usage environment of their right-hand side.



When a variable `x` is linted, if `x` is `Regular`, then it emits the usage environment `[x :-> 1]`. If it's `Alias`, it instead retrieves it's right-hand side usage environment from the `LintM` environment, and emits that instead.


### FunTyCon



`FunTy` is a special case of a `Type`. It is a fully applied function type constructor, so now a function type constructor with five arguments.
This special case is constructed in `mkTyConApp`. The problems come when a `FunTy` is deconstructed, for example `repSplitTyConApp_maybe`, if this
list is not the right length then you get some very confusing errors. The place which was hardest to track down was in `Coercion` where `decomposeFunCo`
had some magic numbers corresponding to the the position of the types of the function arguments.



Look for `Note [Function coercions]` and grep for lists of exactly length 5 if you modify this for whatever reason.


### FunCo



FunCo is modified to take an extra coercion argument which corresponds to coercions between multiplicities. This was added because there was a point to where `mkTyConAppCo` took a coercion as an argument and needed
to produce a `Rig`. It was defaulted to `Omega` for a long time but eventually I changed it to `Coercion`. This seemed to work but there were some problems in CoreLint which mean the check for the role of the coercion
has been commented out for now.



A noteworthy consequence of having an extra argument to `FunTyCon` and `FunCo`, is that some hand-written numbers in the code must change. Indeed, the injectivity of type constructors (that `C a ~ C b` implies `a ~ b`) is implemented by projecting an argument referred by number. This works for `FunCo` too. And it is made use of directly in the code, where the field number is manually written. These field numbers had to be changed. A more robust solution would be to name the projections which are used in the code, and define them close to the definition of `FunCo`.


#### `splitFunTy` and `mkFunTy`.



The core of the implementation is a change to `splitFunTy`. As the arrow now has an associated multiplicity, `splitFunTy` must also return the multiplicity of the arrow. Many changes to the compiler arise from situations where
either `splitFunTy` is called and then we must keep track of the additional information it returns. Further, when there is a call to `mkFunTy`, we must also supply a multiplicity.


### Core to core passes


#### Rebuilding expressions in the optimiser



This subsection is somewhat out of date because let-binders now have alias-like quality to be able to float out. However, everything here still apply for the case-of-case transformation.



There are situations in the optimiser where lets more through cases when case of case is applied.



The simplifier creates let bindings under certain circumstances which
are then inserted later. These are returned in `SimplFloats`.



However, we have to be somewhat careful here when it comes to linearity
as if we create a floating binding x in the scrutinee position.


```
case_w (let x[1] = "Foo" in Qux x) of
  Qux _ -> "Bar"
```


then the let will end up outside the `case` if we perform KnownBranch or
the case of case optimisation.


```
let x[1] = "Foo"
in "Bar"
```


So we get a linearity failure as the one usage of x is eliminated.
However, because the ambient context is an Omega context, we know that
we will use the scrutinee Omega times and hence all bindings inside it
Omega times as well. The failure was that we created a \[1\] binding
whilst inside this context and it then escaped without being scaled.



We also have to be careful as if we have a \[1\] case


```
case_1 (let x[1] = "Foo" in Qux x) of
  Qux x -> x
```


then the binding maintains the correct linearity once it is floated rom
the case and KnownBranch is performed.


```
let x[1] = "Foo"
in x
```


The difficulty mainly comes from that we only discover this context
at a later point once we have rebuilt the continuation. So, whilst rebuilding
a continuation we keep track of how many case-of-case like opportunities
take place and hence how much we have to scale lets floated from the scrutinee.
This is achieved by adding a Writer like effect to the SimplM data type.
It seems to work in practice, at least for T12944 which originally highlighted
this problem.



Why do we do this scaling afterwards rather than when the binding is
created? It is possible the binding comes from a point deep inside the
expression. It wasn't clear to me that we know enough about the context
at the point we make the binding due to the SimplCont type. It might
be thread this information through to get it right at definition site.
For now, I leave warnings and this message to my future self.



For an in-depth discussion see: [
https://github.com/tweag/ghc/issues/78](https://github.com/tweag/ghc/issues/78) and [
https://github.com/tweag/ghc/pull/87](https://github.com/tweag/ghc/pull/87)


#### Pushing function-type coercions



Coercions of kind `a -> b ~ c -> d` are routinely pushed through lambdas or application as follows


```
(f |> co) u  ~~>  (f (u |> co_arg)) |> co_res

(\x -> u) |> co  ~~>  \x' -> u[x\(x |> co_arg)] |> co_res
```


However, this can't always be done when multiplicities are involved: the multiplicity could be coerced (in particular, by `unsafeCoerce`). So, it's possible that the left hand side of these rules is well-typed, while the right hand side isn't. Here is an example of this phenomenon.


```
-- Assuming co :: (Int -> ()) ~ (Int ->. ())

fun x ::(1) Int -> (fun _ -> () |> co) x  ~~>  fun x ::(1) Int -> (fun _ ::(ω) Int -> ()) x
```


To prevent this, we guard this reduction with the condition that the multiplicity component of the coercion is a reflexivity coercion.



I (aspiwack) believe we are only checking whether the coercion is syntactically a reflexivity coercion. This is probably over conservative as coercions are not necessarily fully simplified. We probably need a finer grained test, otherwise this will cause performance regressions.


#### CPR worker/wrapper split


##### Case multiplicity



The CPR split transforms a function


```
f :: A -> B
```


Into a pair


```
$wf :: A -> (# C, D #) -- supposing f's B is built with a constructor with two arguments

f x = case $wf x of
  (# y, z #) -> B y z
```


With linear types, we still need to choose a multiplicity for the case. The correct multiplicity is `1`. It works whether `f` has linear arguments or not. So, the linear transformation is:


```
$wf :: A -> (# C, D #) -- supposing f's B is built with a constructor with two arguments

f x = case_1 $wf x of
  (# y, z #) -> B y z
```


The worker is defined similarly, and also uses a `case_1`.


##### Unrestricted fields



Consider a function


```
f :: Int -> Unrestricted A
```


The argument type doesn't matter, the result type does.



The CPR split yields:


```
$wf :: Int -> (# A #)

f x = case_1 $wf x of
  (# y #) -> Unrestricted y
```


This is ill-typed unless `(# #)` has an unrestricted field (currently, all fields of an unboxed tuple are linear).



The principled solution is to have unboxed tuple be parametrised by the multiplicity of their field, that is


```
type (#,#) :: forall r s. Multiplicity -> Multiplicity -> TYPE r -> TYPE s -> TYPE …

data (#,#) p q a b where
  (#,#) :: a :p-> b :q-> (#,#) p q a b
```


At least the unboxed tuples used by core should have such a type. It can also be a user-facing feature.



At the moment, however, CPR is simply restricted to the case where the constructor only has linear field, precluding some optimisation, but being less intrusive.


## Debugging



If you are debugging then it is very useful to turn on the explicit printing of weights in two places.


1. The outputable instance for `Weighted` in `Weight`.
1. The weight of variables in `Var`, line 309.


There are disabled by default as they affect test output.


## Misc


- Patterns are type checked in a *context multiplicity* which scales the constructor fields, extending the `case_p` from the paper.
- In error messages, `FUN 'Omega` is pretty-printed as `(->)`, just like `TYPE 'LiftedRep` is pretty-printed as `Type` (or `*`).
