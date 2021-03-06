# GHC Plugin Phase Control


## The Problem



We need to be able to specify compiler phases to:


- Be able to specify where user phases from plugins should fit
- Control when rules and inlinings will fire


The current solution where we have phases 0, 1 and 2 is too restrictive to let us do this well, hence this page proposes a generalization that should solve our problems.



I believe the proposed system represents a significant improvement in modularity and usability of phase control within GHC.


## CURRENT STATUS


```wiki
-- $phase_control
-- #phase_control#
--
-- Compiler /core phases/ represent contiguous intervals of time spent in the core pipeline. They are used
-- to control when core-to-core passes, inlining and rule activation take place. The phases @Foo@ and @Bar@
-- would be used in @INLINE@ and @RULES@ pragmas like so:
--
-- > {-# INLINE [Foo] myIdentifier #-}
-- > {-# RULES "myrule" [~Bar] map f (map g xs) = map (f . g) xs #-}
--
-- Phases, with the constraints on when they can run are introduced by the @PHASE@ pragma syntax. So, a phase
-- called @Foo@ that must run before one called @Bar@, after one called @Baz@ and at the same time as one
-- called @Spqr@ is written like so:
--
-- > {-# PHASE Foo < Bar, > Baz, = Spqr #-}
--
-- All the constraints above are so-called /strong/ constraints: there are also /weak/ constraints, which are
-- suggestions but not commands to the compiler as to when to run phases. If, for example, a cycle is formed
-- by including these constraints into the phase partial ordering then the compiler may ignore them, at it's
-- discretion. Weak constraints are indicated by enclosing the constraint in square brackets:
--
-- > {-# PHASE Foo [< Bar], [> Baz] #-}
--
-- Note that it is not currently possible for an equality constraint (phase alias) to be weak.
--
-- Phases may be imported and exported:
--
-- > import MyModule ({-# PHASE Foo #-} {-# PHASE Bar #-})
--
-- There are three built-in phases that are implicitly imported, and these have the special names @0@, @1@ and
-- @2@. These are constrained such that @2@ must run first, then @1@ and finally @0@. The GHC compiler also
-- exports a number of phases from @GHC.Prim@ that correspond to the particular optimizations and analyses
-- that it may run.
--
-- When constructing the core pipeline, GHC gathers all the phases available to it, and then imposes a total
-- order than respects all the strong constraints of those phases and as many of the weak constraints as it
-- wishes. This linearises the core phase graph, which serves as the scaffolding on which the actual Core
-- transformations are hung.
--
-- Now we come to /core passes/, which represent a concrete transformation to be applied to GHCs current Core.
-- Passes will typically just do a single action, but it is possible to specify a \"core pass\" that just runs a
-- list of several other core passes. /Core to-dos/ then associates a core pass with a particular core phase.
--
-- After solving for some linearisation of the available core phases, the compiler takes the core passes associated
-- with each phase in that linearisation by way of a core to-do and concatenates them in order. This yields
-- the final compiler pipeline, which is what is actually run.

-- The above is actually a somewhat simplified picture. It gives the /user model/ for what we do with core phases
-- and passes, but actually we implement a number of optimizations to this process. What is there to optimize? Well,
-- we want to minimize the number of simplifier passes that we perform. A naive approach would be to just do one
-- simplifier run per core phase, to deal with activating the rules and inlinings in that phase. However, this
-- results in a lot of simplification! So, we optimize in three ways:
--
-- 1) When solving for a linearisation of the phases, we group phases together into so-called /phase groups/, which
--    have the property that every phase within the group is depedent only on the phases in other groups. Hence the
--    phases within a phase group are independent, and may be treated by a single simplifier run that "acts as though"
--    it were a simplifier run for every pass individually.
--
-- 2) Before running a simplifier pass introduced for the sole purpose of performing the rules and inlining activations
--    associated with a particular phase, the compiler does a pre-pass to check if there exists any actual uses of that 
--    phase in the source code being compiled. If not, it doesn't bother running that simplifier pass.
--
-- 3) When building up the core pass pipeline, as far as is possible simplifier passes are moved to the end of the part
--    of the core pipeline belonging to their associated phase group. This gives them their maximum effectiveness, while
--    simultaneously allowing us to "coalesce" those simplifier passes that are adjacent into a single simplifier pass
--    that performs the union of the operations of each individual simplifier pass.
```

## Solution


```wiki

module Foo where

{-# PHASE A #-}

{-# RULE "map_map" [A] map f (map g xs) = map (f . g) xs #-}

```


This code has a PHASE declaration which brings a new phase into being. Later rules then use that phase name to control their firing, in contrast to the current system of controlling firing with a limited set of phase numbers.



Phase names have the same name format as data constructors. There is no technical reason for this, as there is never any ambiguity as to whether a name is that of a phase: the choice is purely asthetic and could be changed.



Phase names are exported, so:


```wiki

module Bar where

import Foo

{-# PHASE B < A #-}

{-# RULE "unComp" [B] (f . g) = (\x -> f (g x)) #-}

```


Imports the phase A from Foo and creates its own phase, B, that must occur before A. B is in turn used to control a rule activation.



GHC would gather up the phase ordering constraints from the module it is compiling as well as all imported modules and produce a consistent topological sort. This would be used to order compiler passes and when rules may fire.



What if you want to have an internal phase name that won't clash with other peoples? Then don't export it!


```wiki

module Spqr(..., {-# PHASE C #-}, ...) where

{-# PHASE C < SpecConstr #-}

{-# RULE "silly" [~C] id = (\x -> x) #-}

```


This module explicitly exports its local phase C, which is defined to occur before the [SpecConstr](spec-constr) phase. However the programmer is totally free to remove it from the exports list and hence prevent other modules from referring to it. Likewise, you can selectively import phases:


```wiki

module Baz where

import Spqr({-# PHASE C #-})

{-# PHASE D > C #-}

{-# INLINE [~D] foo #-}

foo = ..

```

## Expressing Dependence



Assuming we just have two levels of ordering we want to express:


- Strict ordering (A MUST appear before/after B)
- Lenient ordering (A SHOULD appear before/after B)


Then a possible syntax is:


```wiki

{-# PHASE A < B, [< C], > D, [> E] #-}

```


To express that A:


- MUST appear before B
- SHOULD appear before C
- MUST appear after D
- SHOULD appear after E


The square brackets are meant to be evocative of optionality in Backus-Naur form, but I'm not yet sure if that is too easily confused with Haskell list syntax.


## Compatability Concerns



There are two principal concerns:


- Code that assumes the current phase control mechanism where we have phases 0, 1 and 2 should still work in this new system
- Compilers that are unable to parse the PHASE pragma should still be able to deal with source code that uses it


To handle these concerns, first we must provide three "wired in" phase names that support the old usage:


```wiki

module Buzz where

{-# PHASE E < 1, > 0 #-}

{-# INLINE [~0] bar #-}
{-# INLINE [E] sqpr #-}

bar = ...
spqr = ...

```


Note that actually the old syntax allowed arbitrary positive integers to be used, not just the set 0-2. However, supporting an infinite set of wired in names is a bit of a headache and I believe that the higher phase numbers were sufficiently rarely used that supporting them is not a major concern. You currently have to supply an additional flag to the compiler (to change the number of simplifier iterations) to even make the higher phases behave differently than phase 2.



The PHASEs 0, 1 and 2 will be implicitly and irrevocably imported into every program GHC compiles. A possible alternate design choice is to have them live in the Prelude, so e.g. you can get rid of them by e.g. explicitly importing the Prelude with an empty import list. This reduces backwards compatability however, and is a little trickier to implement.



Supporting compilers that do not understand the pragma is mostly easy, with the subtelty that we must not require commas between PHASE pragmas that appear in import/export lists. In my opinion we should not even accept such commas on the basis that by doing so would allow users to inadvertently write programs that do not compile on non-GHC and old-GHC compilers.



An example of how it would look is:


```wiki

module Qux({-# PHASE F #-} {-# PHASE G #-} {-# PHASE H #-}) where

import Quux({-# PHASE I #-} {-# PHASE J #-})

... PHASE declarations and uses ...

```


It turns out that we have exactly the required code for this already in the parser to deal with Haddock pragmas.


