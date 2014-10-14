CONVERSION ERROR

Original source:

```trac
== Distriubted closures ==

This page desribes a possible design for `distributed-closure`, a library that implements
serialisable closures, building on top of [wiki:Typeable] and [wiki:StaticPointers].
See the root page [wiki:DistributedHaskell].

The [https://hackage.haskell.org/package/distributed-process distributed-process package] implements a framework for distributed
programming ''à la'' Erlang. Support for static closures is implemented
in a separate package called
[https://hackage.haskell.org/package/distributed-static distributed-static package]. We propose to patch this library in the
following way, and rename it to `distributed-closure`. Ultimately,
distributed-closure should be the one-stop shop for all distributed
frameworks that wish allow users to program with static closures.

== Proposed design == 

`distributed-closure` will define the following datatype:

{{{
data Closure a
}}}

`Closure` is the type of ''static closures''. Morally, it contains some
pointer to a static expression, paired with an environment of only
serializable values.

Why do we need `Closure`? `Closure` is strictly more expressive than
`StaticPtr`. `StaticPtr` can only be constructed from ''closed'' expressions
(no free variables). `Closure` is built on top of `StaticPtr`. It allows
encoding ''serializable expressions''. That is, expressions formed of
only top-level identifiers, literals, and serializable free variables.
for example, using `Closure`, one can write:

{{{
f :: Int -> Int -> ...
f x y = ... closure (static (+)) `closureAp` closurePure x `closureAp` closurePure y ...
}}}

We introduce the following library functions on `Closure`. This is the full API of `distributed-closure`:

{{{
data Closure a

closure :: StaticPtr a -> Closure a
unclosure :: Closure a -> a

closurePure :: Serializable a => a -> Closure a
closureAp :: Closure (a -> b) -> Closure a -> Closure b
}}}

The signature of `closure` mentions `Serializable`, which is a class
defined as follows:

{{{
data Dict c = c => Dict
newtype ctx :- c = Sub (ctx => Dict c)

class (Binary a, Typeable a, Typeable ctx, ctx :=> Serializable a) => Serializable a where
  serializableDict :: StaticPtr (ctx :- Serializable a)
}}}

In words, a ''serializable value'' is a value for which we have
a `Binary` instance and a `Typeable` instance, but moreover for which
we can obtain a `StaticPtr` referencing a ''proof that the set of constraints `ctx` entails `Serializable a`''. (The `Dict` datatype and `(:-)` can be obtained from the [http://hackage.haskell.org/package/constraints constraints package] on Hackage). In other words, a value of type `ctx :- Serializable a` is ''explicit'' evidence that GHC's instance resolution can derive `Serializable a` from `ctx`. The constraint `ctx :=> Serializable a` is what allows reifying this evidence of instance resolution.

The above is useful for the implementation of `closurePure`.

== Implementation

=== Implementation in GHC

TODO See [https://ghc.haskell.org/trac/ghc/wiki/StaticPointers/Old old proposal] and [https://ghc.haskell.org/trac/ghc/blog/simonpj/StaticPointers blog post] by Simon PJ.

=== Implementation of `distributed-closure`

The definition of `Closure a` is as follows:

{{{
data Closure a where
  StaticPtr :: StaticPtr a -> Closure a
  Encoded :: ByteString -> Closure ByteString
  Ap :: Closure (a -> b) -> Closure a -> Closure b
}}}

This definition permits an efficient implementation: there is no need
to reserialize the environment everytime one composes two `Closures`s.
The definition in the Cloud Haskell paper is as follows:

{{{
data Closure' a where
  Closure' :: StaticPtr (ByteString -> a) -> ByteString -> Closure a
}}}

Note that the `Closure'` constructor can be simulated:

{{{
Closure cf env <=> Ap (StaticPtr cf) (Encoded env)
}}}

One can even add the following constructor for better efficiency:

{{{
data Closure a where
  ...
  Closure :: Closure a -> a -> Closure a
}}}

Any `StaticPtr` can be lifted to a `Closure`, and so can any
serializable value:

{{{
closure :: StaticPtr a -> Closure a
closure x = StaticPtr 

closurePure :: Serializable a => a -> Closure a
closurePure x =
    StaticPtr (static decodeD) `closureAp`
    serializableDict Proxy `closureAp`
    Encoded (encode x)
  where
    decodeD :: Dict (Serializable a) -> ByteString -> a
    decodeD Dict = decode
}}}

Given any two `Closure`s with compatible types, they can be combined
using `closureAp`:

{{{
closureAp :: Closure (a -> b) -> Closure a -> Closure b
closureAp = Ap
}}}

Closure serialization is straightforward, but closure deserialization
is tricky. See
[https://ghc.haskell.org/trac/ghc/blog/simonpj/StaticPointers#Serialisingstaticpointers this blog post section] from Simon PJ as to why. The issue is that
when deserializing from a bytestring to target type `Closure b`, one
needs to ensure that the target type matches the type of the closure
before it was serialized, lest ''bad things happen''. We need to impose
that `Typeable b` when deserializing to `Closure b`, but that doesn't
help us for all closures. Consider in particular the type of `Ap`:

{{{
Ap :: Closure (a -> b) -> Closure a -> Closure b
}}}

Notice that the type `a` is not mentioned in the return type of the
constructor. We need to know `Typeable (a -> b)` and `Typeable a` in
order to recursively deserialize the subclosures, but we can't infer
either from the context `Typeable b`. The trick is to introduce
`ApDyn` and redefine `closureAp`:

{{{
newtype DynClosure = DynClosure Dynamic

data Closure a where
  ...
  ApDyn :: DynClosure -> DynClosure -> Closure b

closureAp :: (Typeable a, Typeable b) => Closure (a -> b) -> Closure a -> Closure b
closureAp cf cx = ApDyn (DynClosure (toDynamic cf)) (DynClosure (toDynamic cx))
}}}

'''Note: this far from the only solution and is not ideal (it repeats information the receiving end already knows). Alternative plan forthcoming.'''

`DynClosure` is ''not'' a public type so we can assume whatever
invariants we like: the user can't build any values of this type
directly. One can serialize/deserialize a `DynClosure` quite easily:

{{{
instance Binary DynClosure where
  put (DynClosure (Dynamic typerep x)) =
      -- XXX Can't use Any because no Typeable Any.
      let clos :: Closure () = unsafeCoerce x
      in encode clos
  get bs = do
    typerep <- get
    clos :: Closure () <- get
    return $ DynClosure $ Dynamic typerep x
}}}

From whence we can have

{{{
instance Typeable a => Binary (Closure a) where
  put = ... -- Does not use the ambient Typeable constraint.
  get = ... -- Uses ambient Typeable constraint to check we are
            -- deserializing against the right type.
}}}

We only need the `Typeable` constraint when deserializing, but not
during deserialization, because the smart constructors `closurePure`,
`closureAp` etc enforce that any `Closure a` has `Typeable a` by
construction.

The occurrence of `unsafeCoerce` above is quite ok: it is only used to
recover structure from the wrapped `Dynamic`: that the type of the
object stored in the `Dynamic` is in fact always of the form `Closure
b` for some `b`. We are allowed to pretend `b == ()` always, just as
`Dynamic` internally pretends that its content is of type `Any`. This
structure is an invariant that we make sure to have the pubic API of
our module enforce.

All that remains is to implement `unclosure`:

unclosure :: Typeabe a => Closure a -> a
unclosure (StaticPtr sptr) = unstatic sptr
unclosure (Encoded x) = x
unclosure (Ap cf cx) = (unstatic cf) (unstatic cx)
unclosure (ApDyn (DynClosure dyncf) (DynClosure dyncx)) = dynApply dyncf dyncx
unclosure (Closure cx x) = x

=== About performance

We anticipate that the dynamic type checks associated with the use of
`Dynamic` may have a substantial impact on performance. Not only that,
the presence of these `Dynamic`s bloats the size of the messages that
are sent over the wire. But one nice property of this approach is that
we can always keep ''both'' `Ap` and `ApDyn` constructors, and define
`unsafeClosureAp` as: 

{{{
unsafeClosureAp :: Typeable a => Closure (a -> b) -> Closure a -> Closure b
unsafeClosureAp = Ap
}}}

`unsafeClosureAp` is used to send composite `Closure`s over the wire
''without'' dynamic type checks. This in general may allow crafting
messages that cause the remote side to segfault, but that's what the
name is all about. And the remote side is free to refuse processing
`Closure`s built with `unsafeClosureAp` if it doesn't trust the
sender.

== Conclusion

It appears possible to implement a language extension first proposed
in the original Cloud Haskell paper in a way that supports polymorphic
types - a feature that was not considered in the paper. Furthermore,
the proposal in the original Cloud Haskell paper compromised type
safety since it allowed deserializing `Closure`s at arbitrary type,
while this proposal adds extra safety yet still making it possible to
use a backdoor for performance.

What's the trusted code base (TCB) that you need to trust in order to
guarantee type safety? GHC of course, but not only. This language
extension adds one new primitive function to GHC. But one needs to
also trust `dynamic-closure`, since it uses `unsafeCoerce`. Ideally
one would only have to trust GHC and its standard libraries, and have
`dynamic-closure` be part of the standard library. But in any case
`dynamic-closure` depends on at least `binary` in order to do its
work, which it would be undesirable to pull into `base`, so is best
kept separate from GHC.
```