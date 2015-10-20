


# `OverloadedLabels`



This page describes the `OverloadedLabels` extension, part 2 of the [OverloadedRecordFields proposal](records/overloaded-record-fields).


### Digression: implicit parameters



First, let's review Haskell's existing and long-standing *[
implicit parameters](https://downloads.haskell.org/~ghc/latest/docs/html/users_guide/other-type-extensions.html#implicit-parameters)*.
Here is how they work in GHC today.


- There is a class `IP` defined thus in `GHC.IP`:

  ```wiki
  class IP (x :: Symbol) a | x -> a where
    ip :: a

  -- Hence ip's signature is
  --    ip :: forall x a. IP x a => a
  ```
- When you write `?x` in an expression, what GHC does today is to replace it with `(ip @ "x" @ alpha)`, where `alpha` is a unification variable and `@` is type application.  (This isn't valid source Haskell, which doesnt have type application, but GHC certainly does have type application internally, so we don't need proxy arguments here.

- Of course, that call `(ip @ "x" @ alpha)` gives rise to a constraint `IP "x" alpha`, which must be satisfied by the context.

- The form `?x` in an expression is only valid with `{-# LANGUAGE ImplicitParameters #-}`

- The pretty printer displays the constraint `IP x t` as `?x::t`.

- The functional dependency `x->a` on class `IP` implements the inference rules for implicit parameters. (See the [
  orginal paper](http://galois.com/wp-content/uploads/2014/08/pub_JL_ImplicitParameters.pdf).)

- There is some magic with implicit-parameter bindings, of form `let ?x = e in ...`, which in effect brings into scope a local instance declaration for `IP`.


And that's really about it.  The class `IP` is treated specially in a few other places in GHC.  If you are interested, grep for the string "`isIP`".


### Overloaded labels



Now consider the following class:


```wiki
class IsLabel (x :: Symbol) a where
  fromLabel :: a
```


Exactly like `IP` but without the functional dependency. It is also rather similar to a version of the `IsString` class from `OverloadedStrings`, but with an additional parameter making the string available at the type level.



It behaves like this:


- When you write `#x` in an expression, what GHC does is to replace it with `(fromLabel @ "x" @ alpha)`, where `alpha` is a unification variable and `@` is type application.   Just like implicit parameters, in fact.

- Of course the call `(fromLabel @ "x" @ alpha)` gives rise to a constraint `(IsLabel "x" alpha)` which must be satisfied by the context.

- The form `#x` in an expression is only valid with `{-# LANGUAGE OverloadedLabels #-}` (which is implied by `OverloadedRecordFields`).

- The pretty printer could print `IsLabel "x" t` as `#x::t` (**AMG**: I don't plan to implement this initially).

- There is no functional dependency, and no equivalent to the implicit-parameter `let ?x=e` binding.  So overloaded labels are much less special than implicit parameters.


Notice that overloaded labels might be useful for all sorts of things that are nothing to do with records; that is why they don't mention "record" in their name.



User code can never (usefully) call `fromLabel` (or `ip`) directly, because without explicit type application there is no way to fix `x`.

