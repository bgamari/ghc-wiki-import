CONVERSION ERROR

Original source:

```trac
= Adding kind equalities to GHC =

This page is a discussion of the implementation of kind equalities in GHC. It is meant as a guide to the interesting bits.

== Points of interest ==

=== `TyVar` --> `TyCoVar` ===

In many functions and datatypes throughout GHC, I changed names including
`TyVar` to names include `TyCoVar`. These functions/types now may contain
coercion variables as well as type variables. The name change does two
things: it calls my attention to these functions when something changes
during a merge, and the new name reminds me (and, potentially, others
someday) to handle both type variables and coercion variables.

=== All coercion variables are Pi-bound ===

What is the type of `\ (c :: Int ~# Bool). 5 |> c`? In theory, it could be
`(Int ~# Bool) -> Bool` or `forall (c :: Int ~# Bool). Bool`. I always choose
the latter, to make `exprType` sane. That `forall` should really be spelled `pi`.

=== `Binder` ===

`Type` now has merged `FunTy` and `ForAllTy`. Here is the declaration for the
new `ForAllTy`:

{{{
  | ForAllTy Binder Type   -- ^ A Π type.
}}}

with

{{{
-- | A 'Binder' represents an argument to a function. Binders can be dependent
-- ('Named') or nondependent ('Anon'). They may also be visible or not.
data Binder
  = Named Var VisibilityFlag
  | Anon Type   -- visibility is determined by the type (Constraint vs. *)
}}}

The `Binder` type is meant to be abstract throughout the codebase. The only substantive difference between the combined `ForAllTy` and the separate `FunTy`/`ForAllTy` is that we now store visibility information. This allows use to distinguish between, for example

{{{
data Proxy1 (a :: k) = P1
}}}

and

{{{
data Proxy2 k (a :: k) = P2
}}}

`Proxy1`'s kind argument is `Invisible` and `Proxy2`'s is `Visible`.

Currently, any `Named` `ForAllTy`s classifying ''terms'' are all `Invisible`.

This design change has a number of knock-on effects. In particular, `splitForAllTys` now splits regular functions, too. In some cases, this actually simplified code. In others, the code had to use the new `splitNamedForAllTys`, which is equivalent to the old `splitForAllTys`.

Another knock-on effect is that `mkForAllTy` now takes a `Binder`. To make this easier for callers, there is a new `mkInvForAllTy :: TyCoVar -> Type -> Type` which makes a `Named`, `Invisible` `Binder` for the `ForAllTy`.

In general, I've been happy with this new design. In some preliminary work toward Pi, `Binder` has added a few more bits, making this redesign even better going forward.

Previously, we've thought about adding visibility information to the anonymous case. I still think this is a good idea. I just haven't done it yet.

=== Kind equalities and data constructors ===

'''Universal variables are type variables; existentials might be coercion variables'''

A type constructor's type variables are just that: they are sure to be proper
type variables. There doesn't seem to be anything wrong, in theory, with including
coercion variables here, but there also doesn't seem to be a need. However,
a data constructor's ''existential'' variables might be coercions. Indeed,
this is how all GADTs are currently encoded. For example:

{{{
data G1 a where
  MkG1 :: Int -> G1 Bool
data G2 (a :: k) where
  MkG2 :: Char -> G2 Double
}}}

The rejigged types look like this:

{{{
MkG1 :: forall (a :: *). forall (gadt :: a ~# Bool). Int -> G1 a
MkG2 :: forall (k :: *) (a :: k).
        forall (gadt1 :: k ~# *) (gadt2 :: a |> gadt1 ~# Double).
        Char -> G2 k a
}}}

Thus, a `TyCon` can have coercion-variable arguments, but only if that
`TyCon` is really a promoted datacon.

'''Separation between dependent and non-dependent equalities'''

Various bits of code refer to dependent vs. non-dependent equalities. A "dependent
equality" is a coercion that is used in a type; a non-dependent equality is not
used in a type. At one point, I was thinking that a GADT datacon should be careful
to distinguish between dependent equalities and non-dependent ones. That way,
we could defer type errors for non-dependent equalities by using a lifted coercion
instead of an unlifted one there. But, now I think everything should just use
unlifted equality and that we should remove this distinction. Bottom line: don't
worry about this too much.

'''GADT coercions are now existential variables'''

In accordance with the two points above, all GADT-induced coercions are now considered
existential variables. This causes a little work around datacon signatures, because
a signature includes a separate field for existential variables as it does for GADT
equalities. This could be cleaned up somewhat, now that I've decided that all GADT
equalities really should be existentials.


=== `tryTcS` is now really pure ===

In HEAD, `tryTcS` claims to "throw away all evidence generated". This isn't quite true. `tryTcS` can still set metavariables and may twiddle `EvBindsVar`s inside of implications. With kind equalities, this won't do. The problem is that solving may invent new coercion variables; these variables may end up in types. If a metavariable is then set to point to a type with a fresh coercion variable in it, we have a problem: after throwing away the evidence, that coercion variable is unbound. (This actually happens in practice.) So, `tryTcS` must be very careful to be properly pure. It does this by maintaining the set of filled-in metavariables ''and'' a way to roll back any changes to nested `EvBindsVar`s. After the inner `TcS` action is complete, `tryTcS` rolls back the changes.

This works nicely in practice, but one does have to be careful when reading a `-ddump-tc-trace`, because a `writeMetaTyVar` might not be the final word. (If that's an issue, it's easy to fix. The `TcS` monad could know whether it's pure or not and print out accordingly.)

=== CUSKs ===

I have a sinking feeling that a type has a CUSK now only when all types '''and kinds''' have known types. But I can't come up with an example that shows this clearly. However, we can say that anything to the right of a `::` is known to have type `*`, so this doesn't bite hard in practice. Thus `data T (a :: k)` has a CUSK, but `data S (a :: Proxy k)` does not. Does `data U (a :: Maybe k)`? I think it does, but that's not quite as obvious. What's the easy-to-articulate rule here? (Now, it's this nice rule: a type has a CUSK iff all of its type variables are annotated; if it's a closed type family, the result kind must be annotated, too.)

=== Datacon wrappers are now rejigged ===

In HEAD, a datacon worker differs from a datacon wrapper in two distinct ways: the worker's types are `UNPACK`ed as requested, and the worker's type is rejigged, à la
`rejigConRes`. The wrapper has the datacon's original type.

This design caused endless headaches for me. (Sadly, I can't recall exactly what the problem was -- something to do with applying kind substitutions to variables. I can easily recall going round and round trying to figure out the right datacon design, though!) So, I changed wrappers to have a rejigged type. (Workers are unchanged.) This was actually a nice simplification in several places -- specifically in GADT record update. The only annoying bit is that we have to be careful to print out the right user-facing type, which is implemented in `DataCon.dataConUserType`.

=== Fewer optimizations in zonking ===

There are a few little optimizations in !TcHsSyn around zonking. For example, after finding a filled-in metavariable, its contents are zonked and then the variable is re-set to the zonked contents. This is problematic now.

The zonking algorithm in !TcHsSyn knot-ties `Id`s. Of course, coercion variables are `Id`s, and so these knot-tied bits can appear in types. We thus must be very careful never, ever to look at a zonked type, which these optimizations do. So, I removed them.

I have not yet re-examined to see if there is a way to restore this behavior. There probably is, as coercion variables can't be recursive!

=== `MaybeNew` is back ===

In Simon's refactoring in fall 2014, the `MaybeNew` type disappeared from the solver infrastructure. I found this type useful, so I brought it back. It seemed like a better way to structure my algorithm than working without it.

=== Lots more "`OrCoVar`" functions in `Id` module ===

A `CoVar` is now a distinct flavour of an `Id`, with its own `IdDetails`. This is necessary because we often want to see -- quickly -- whether or not a var is a covar. However, there are many places in the code that creates an `Id`, without really knowing if the `Id` should be a plain old `Id` or really a `CoVar`. There are also a bunch of places where we're sure it's really not a `CoVar`. The `OrCoVar` functions allow call sites to distinguish when the `CoVar`-check (done by looking at a var's type) should be made. This is not just an optimization: in one obscure scenario (in the simplifier, if I recall), the type is actually a panic.

This could stand some cleaning up, but it was hard for me to figure out when we're sure an `Id` isn't a `CoVar`.

=== No more `instance Eq Type` ===

Somewhere along the way (I think in wildcard implementation?), an `instance Eq Type` slipped in. I removed it.

=== `analyzeType` ===

Once upon a time, I embarked on a mission to reduce imports of `TyCoRep`, instead aiming to export functions to make exposing `Type`'s innards unnecessary. This effort became `analyzeType` and `mapType`, both in `Type.hs`. `mapType` is a clear win, removing gobs of zonking code and making a relatively clean interface. See simplifications in !TcHsSyn and TcMType. It's not clear if `analyzeType` is paying its weight though. I could easily undo this change.

== Tasks ==

* Fully remove non-dependent GADT equalities.

* Try to restore optimizations in zonking.

* Check kind variables when determining whether or not a declaration has a CUSK.
```