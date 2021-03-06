CONVERSION ERROR

Original source:

```trac
== Distributed closures ==

This page desribes a possible design for `distributed-closure`, a library that implements
serialisable closures, building on top of [wiki:Typeable] and [wiki:StaticPointers].
See the root page [wiki:DistributedHaskell].

The [https://hackage.haskell.org/package/distributed-process distributed-process package] implements a framework for distributed
programming ''à la'' Erlang. Support for static closures is implemented in a separate package called
[https://hackage.haskell.org/package/distributed-static distributed-static package]. We propose to patch this library in the
following way, and rename it to `distributed-closure`. Ultimately, distributed-closure should be the one-stop shop for all distributed frameworks that wish to allow users to program with static closures.

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
class (Binary a, Typeable a) => Serializable a where
  serializableDict :: Closure (Dict (Serializable a))
}}}

'''NOTE: this definition of `Serializable` is different from the current one as found in `distributed-process`, and also different from the one DistributedHaskell#Serialization.'''

In words, a ''serializable value'' is a value for which we have
a `Binary` instance and a `Typeable` instance, but moreover for which
we can obtain a static proof of serializability, in form of a dictionary.

One could make do with an empty class definition for `Serializable`, as is currently the case, but without this augmented definition, lifting arbitrary serializable values to `Closure`s becomes much less convenient (see `closurePure` below).

One might argue that `Serializable` is a tad more complex than it could be. The central issue is that the `-XStaticPointers` extension alone does not offer any means to reify a constraint to ''static'' evidence in the form of a dictionary - a feature that is in general very useful indeed (see below). In principle this could be done, since dictionaries are morally either static, or simple combinations of static dictionaries, but it would require a further extension to the compiler, possibly to the type system itself. The above definition of `Serializable` comes at the economy of such a compiler extension.

Below are some example instances:

{{{
instance Serializable Int where
  serializableDict = closure $ static Dict

instance Serializable (Maybe Int) where
  serializableDict = closure $ static Dict

instance Serializable b => Serializable (Either String b) where
  serializableDict =
    closure (static \Dict -> Dict) `closureApply` serializableDict

data Foo = Foo deriving (Generic, Typeable)

instance Binary Foo
instance Serializable Foo where
  serializableDict = static Dict

-- Datatype of state transitions, where the state s need not be serializable.
data Command s = Transition (Closure (s -> s))
               | Stop

instance Binary Command
instance (Typeable s) => Serializable (Command s) where
  -- NOTE: Requires the TTypeRep proposal.
  serializableDict =
    closure (static (`withTypeable` Dict)) `closureApply` closurePure tTypeRep
}}}

Notice the pay-as-you-go nature of the instances. Only in instances with polymorphic heads do you need to compose dictionaries by hand. Most users do not have type parameterized data types that they want to serialize. Those users never need to write more than simple one liner instances.

== Implementation

=== Implementation in GHC

TODO See [StaticPointers/Old old proposal] and [https://ghc.haskell.org/trac/ghc/blog/simonpj/StaticPointers blog post] by Simon PJ.

=== Implementation of `distributed-closure`

The definition of `Closure a` is as follows:

{{{
data Closure a where
  StaticPtr :: StaticPtr b -> Closure b
  Encoded :: ByteString -> Closure ByteString
  Ap :: Closure (b -> c) -> Closure b -> Closure c
}}}

This definition permits an efficient implementation: there is no need
to reserialize the environment everytime one composes two `Closures`s.
The definition in the Cloud Haskell paper is as follows:

{{{
data Closure' a where
  Closure' :: StaticPtr (ByteString -> b) -> ByteString -> Closure b
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
    serializableDict `closureAp`
    Encoded (encode x)
  where
    decodeD :: Dict (Serializable a) -> ByteString -> a
    decodeD Dict = decode
}}}

'''Side note:'''
What if we didn't have `serializableDict`? What if we reverted to the current definition of `Serializable`? Then it would be impossible to write `closurePure` with the above signature. We would need to pass in explicitly some ''static'' serialization dictionary. This is quite a bother, since it forces us to introduce spurious top-level bindings for dictionaries, e.g.:

{{{
closurePure' :: Static (Dict (Serializable a)) -> a -> Closure a
closurePure' sdict x = StaticPtr sdict `closureAp` Encoded (encode x)

sdictInt :: Static (Dict (Serializable a))
sdictInt = static Dict

thirtyClosure = plusClosure `closureAp` closurePure sdictInt 10 `closureAp` closurePure sdictInt 20
}}}

'''End side note.'''

Given any two `Closure`s with compatible types, they can be combined
using `closureAp`:

{{{
closureAp :: Closure (a -> b) -> Closure a -> Closure b
closureAp = Ap
}}}

Notice how `Closure` is ''nearly'' the free applicative functor, though not completely free, because we impose `Serializable a` in `closurePure`. It ''is'', however, a [https://hackage.haskell.org/package/semigroupoids semigroupoid].

Closure serialization is straightforward, but closure deserialization
is tricky. See
[https://ghc.haskell.org/trac/ghc/blog/simonpj/StaticPointers#Serialisingstaticpointers this blog post section] from Simon PJ as to why. The issue is that
when deserializing from a bytestring to target type `Closure b`, one
needs to ensure that the target type matches the type of the closure
before it was serialized, lest ''bad things happen''. We need to impose
that `Typeable b` when deserializing to `Closure b`, but that doesn't
help us for all closures. Consider in particular the type of `Ap`:

{{{
Ap :: Closure (b -> c) -> Closure b -> Closure c
}}}

Notice that the type `b` is not mentioned in the return type of the
constructor. We need to know `Typeable (b -> c)` and `Typeable b` in
order to recursively deserialize the subclosures, but we can't infer
either from the context `Typeable c`.

There are solutions to this problem.

'''Alternative 1:'''

The trick is to realize that in fact the type `b` does not matter: it could be arbitrary. After all, that's why it appears existentially quantified in the type of the `Ap` constructor. Type safety guarantees that the `Ap` constructor always combines two `Closure`s of compatible type. In other words, the type of the first argument of `Ap` could as well be taken to be `Closure (Any -> c)`, because any lifted type can be coerced to/from `Any`, so that any value of type `b` can reasonably also be ascribed type `Any`. Since we don't care about the type `b`, might as well take it to be `Any`. If one trusts closure serialization, and indeed closure serialization must be part of the TCB (see DistributedHaskell#Thetrustedcodebase), then when deserializing a closure, we can reconstruct an `Ap` node, taking `b ~ Any`, or equivalently, always deserializing at the following type:

{{{
Ap :: Closure (Any -> c) -> Closure Any -> Closure c
}}}

'''Note:''' `Any` ''must'' have a `Typeable` instance. This is the case in GHC 7.8, but in GHC >= 7.9, `Any` is now a type family with no instance, hence cannot be given a `Typeable` instance (see tickets #9429). Deserialization can go something along the following lines (beware, highly idealized code):

{{{
decodeClosure :: forall a. Typeable a => ByteString -> (ByteString, Closure a)
decodeClosure (B.uncons -> '0':bs) = (StaticPtr <$> decodeStaticPtr) bs
decodeClosure (B.uncons -> '1':bs) = (Encoded <$> decodeByteString) bs
decodeClosure (B.uncons -> '2':bs) = (do
    cf <- decodeClosure :: Any -> a
    cx <- decodeClosure :: Any
    return $ ClosureAp cf cx
    ) bs
}}}

Now it may often happen that `decodeStaticPtr` will be called against type `StaticPtr Any`, or `StaticPtr (Any -> c)`. `decodeStatic` internally compares `TypeRep`s before producing a result: it compares the `TypeRep` of the target type against the type found in the SPT. With the above definition of `Ap`, the `TypeRep`s in general will not match exactly: we will be comparing the `Typerep` of `Any -> c` to that of `b -> c` for some type `b`. For this to work, '''we need to carry out `TypeRep` matching modulo `Any`'''. More precisely, we require the following laws (for some `Typeable b`):

{{{
isJust (cast :: Any -> Maybe b) == True
isJust (cast :: b -> Maybe Any) == True
}}}

Insofar as `Any` is an internal compiler type not normally accessible to the user, the above should not compromise type safety.

'''Alternative 2:'''

{{{
-- Like dynApply, but for things of type Closure a.
dynClosureApply :: Dynamic -> Dynamic -> Dynamic
dynClosureApply x y = error "TODO"

decodeClosure :: Typeable a => ByteString -> (ByteString, Closure a)
decodeClosure = fromDyn <$> dyn
  where
    dyn :: ByteString -> (ByteString, Dynamic)
    dyn ('0':bs) = (do
        DS (sptr :: StaticPtr a) <- decodeStatic bs
        return $ toDyn $ StaticPtr sptr) bs
    dyn ('1':bs) = (toDyn . Encoded <$> decodeByteString) bs
    dyn ('2':bs) = (dynClosureApply <$> dyn <*> dyn) bs
}}}

Assuming this proposal for [wiki:Typeable Typeable], it should be possible to implement `dynClosureApply` in a type safe way, without using `unsafeCoerce` internally. Otherwise `dynClosureApply` will have to be part of the TCB.

'''End of alternatives.'''

All that remains is to implement `unclosure`:

{{{
unstatic :: StaticPtr a -> a

unclosure :: Closure a -> a
unclosure (StaticPtr sptr) = unstatic sptr
unclosure (Encoded x) = x
unclosure (Ap cf cx) = (unstatic cf) (unstatic cx)
unclosure (Closure cx x) = x
}}}

'''Tradeoffs:'''

Alternative 1:
- requires a `Typeable` instance for `Any`.
- requires type casting modulo `Any`.
- Potentially faster: dynamic type checks done only once per `StaticPtr` in `decodeClosure`.
- Makes `decodeClosure` part of the TCB.

Alternative 2:
- Can be be implemented outside the TCB, but requires the [wiki:Typeable Typeable] proposal to do so.
- Potentially slower: dynamic type checks at every `Ap` node when doing `unDynClosure`.

=== About performance

In the case of monomorphic functions, there need be no `TypeRep` sent over the wire in the scheme presented above, yet we still preserve type safety. `TypeRep`s bloat the messages sent on the wire, so their absence can only be a good thing for performance. That said, we anticipate that even the dynamic type checks performed by `decodeStatic` can be detrimental for performance. For this reason, there ought to be a

{{{
unsafeDecodeStaticPtr :: ByteString -> (ByteString, StaticPtr a)
unsafeDecodeClosure :: ByteString -> (ByteString, Closure a)
unsafeDecodeClosure = ... unsafeDecodeStaticPtr ...
}}}
that perform no dynamic type checks. This of course may come at the cost of some type safety, but only if the user somehow writes something equivalent to `decodeClosure (encodeClosure (x :: Closure a)) :: Closure b`, or if the remote side crafts devious applications of two closures of incompatible type. In high performance settings, one can often make the simplifying assumption that peers can be trusted, so this should not be a problem in practice.

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
also trust `decodeStaticPtr`, and that's it. Note that if `decodeStaticPtr` is implemented in terms of `Binary`, then Ideally
one would only have to trust GHC and its standard libraries, and have
`dynamic-closure` be part of the standard library. But in any case
`dynamic-closure` depends on at least `binary` in order to do its
work, which it would be undesirable to pull into `base`, so is best
kept separate from GHC.
```
