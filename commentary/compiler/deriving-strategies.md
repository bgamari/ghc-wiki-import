# Deriving strategies






Deriving strategies are a proposed feature to grant users finer-grained control over how instances may be derived.


## Motivation



GHC Trac [\#10598](https://gitlab.staging.haskell.org/ghc/ghc/issues/10598) revealed a limitation of GHC's current instance deriving mechanism. Consider the following program which uses both `DeriveAnyClass` and `GeneralizedNewtypeDeriving`:


```
{-# LANGUAGE DeriveAnyClass, GeneralizedNewtypeDeriving #-}

class C a where
  c :: a -> String
  c _ = "default"

instance C Int where
  c = show

newtype T = MkT Int deriving C
```


What `C` instance should be derived for `T`? GHC could use `GeneralizedNewtypeDeriving` and use the underlying instance for `Int`. On the other hand, GHC could just as well use `DeriveAnyClass` and give `T` a default implementation for `c`! We've uncovered an ambiguity.



Currently, GHC will accept the above code by defaulting to the `DeriveAnyClass` strategy (after emitting a warning). This is an unfortunate outcome, because it now
prevents users from using `GeneralizedNewtypeDeriving` and `DeriveAnyClass` simultaneously.



There are some other shortcomings of instance deriving as well. For instance, one cannot derive "newtype-style" `Read` or `Show` instances. For example:


```
{-# LANGUAGE GeneralizedNewtypeDeriving #-}

newtype S = MkS Int deriving Show

sOne :: String
sOne = show (MkS 1)
```


Despite our best efforts, the value of `sOne` will be `MkS 1` instead of `1`. The behavior of a `deriving Show` clause is to always produce a `Show` instance that includes the name of the constructor, even if `GeneralizedNewtypeDeriving` is on. While this is usually what you want, there are rare occasions where you simply want to use the underlying type's `Show` instance instead of constructing an entirely new one. Unfortunately, GHC does not give you a way to express this.


## Deriving strategies



A solution to the above issues is to introduce a syntax extension called *deriving strategies*. They are named as such because they allow users to state explicitly in a deriving clause what approach GHC should take when attempting to derive an instance for a typeclass. There are currently three strategies that GHC is aware of:


- Deriving bespoke instances: This is the usual approach that GHC takes. For certain classes that GHC is aware of, such as `Eq`, `Ord`, `Functor`, `Generic`, and others, GHC can use an algorithm to derive an instance of the class for a particular datatype. For example, a bespoke, derived `Eq` instance for `data Foo = Foo Int` is:

```
instance Eq Foo where
  Foo a == Foo b = a == b
```

>
>
> It is "bespoke" in the sense that GHC tailor-made the implementation of the instance to fit the definition of `Foo`. The bespoke strategy only requires enabling language extensions in certain cases (`DeriveFunctor`, `DeriveGeneric`, etc.).
>
>

- `GeneralizedNewtypeDeriving`: An approach that GHC only uses if the eponymous language extension is enabled, and if an instance is being derived for a newtype. GHC will reuse the instance of the newtype's underlying type to generate an instance for the newtype itself. For more information, see [
  http://downloads.haskell.org/\~ghc/8.0.1/docs/html/users\_guide/glasgow\_exts.html\#generalised-derived-instances-for-newtypes](http://downloads.haskell.org/~ghc/8.0.1/docs/html/users_guide/glasgow_exts.html#generalised-derived-instances-for-newtypes)
- `DeriveAnyClass`: An approach that GHC only uses if the eponymous language extension is enabled. When this strategy is invoked, GHC will simply generate an instance with empty implementations for all methods. For more information, see [
  http://downloads.haskell.org/\~ghc/8.0.1/docs/html/users\_guide/glasgow\_exts.html\#deriving-any-other-class](http://downloads.haskell.org/~ghc/8.0.1/docs/html/users_guide/glasgow_exts.html#deriving-any-other-class)


While GHC can pick a strategy internally, users don't have a reliable way to pick a strategy other than enabling language extensions and hoping that GHC does the right thing (which it often doesn't, as evidenced in the above problematic examples). The deriving strategies proposal aims to:


1. Introduce a new `-XDerivingStrategies` language extension
1. Allocate three keywords (`bespoke`, `newtype`, and `anyclass`) that can be used in `deriving` clauses or standalone `deriving` declarations to indicate which strategy to use when `-XDerivingStrategies` is enabled
1. Allow datatypes to have multiple `deriving` clauses when `-XDerivingStrategies` is enabled

### Examples



Here is an example showing off what `-XDerivingStrategies` allows:


```
{-# LANGUAGE DeriveAnyClass #-}
{-# LANGUAGE DeriveFoldable #-}
{-# LANGUAGE DeriveFunctor #-}
{-# LANGUAGE DerivingStrategies #-}
{-# LANGUAGE GeneralizedNewtypeDeriving #-}
{-# LANGUAGE StandaloneDeriving #-}

newtype T a = T a
  deriving          Show
  deriving bespoke  (Eq, Foldable)
  deriving newtype  Ord
  deriving anyclass Read

deriving bespoke instance Functor T
```


This demonstrates why part 3 is important: with multiple `deriving` clauses, one can fine-tune which instances should be derived with particular deriving strategies.


### The deriving strategy resolution algorithm



With `-XDerivingStrategies` in the picture, we can now state how GHC figures out which deriving strategy to use for a particular derived instance:


1. Look for a deriving strategy. If one is present, use that.

1. (a) If deriving an `Eq`, `Ord`, `Ix`, or `Bounded` instance for a newtype, use the `GeneralizedNewtypeDeriving` strategy (even if the language extension isn't enabled).

>
>
> (b) If deriving a `Read`, `Show`, `Data`, `Generic`, `Generic1`, `Typeable`, `Traversable`, or `Lift` instance for a newtype, go to step 3.
>
>

>
>
> (c) Otherwise, if deriving a "standard derivable class" (e.g., `Eq`, `Ord`, `Generic`, etc.) instance for a newtype, and `-XGeneralizedNewtypeDeriving` is enabled, derive the class using the `GeneralizedNewtypeDeriving` strategy.
>
>

>
>
> (d) Otherwise, if deriving an instance for a newtype and both `-XGeneralizedNewtypeDeriving` and `-XDeriveAnyClass` are enabled, default to `DeriveAnyClass`, but emit a warning stating the ambiguity.
>
>

>
>
> (e) Otherwise, if deriving an instance for a newtype, the datatype and typeclass can be successfully used with `GeneralizedNewtypeDeriving`, and `-XGeneralizedNewtypeDeriving` is enabled, do so.
>
>

1. (a) If deriving a "standard derivable class" (e.g., `Eq`, `Ord`, `Generic`, etc.) and the corresponding language extension is enabled (if necessary), do so. If the language extension is not enabled, throw an error.

>
>
> (b) Otherwise, if `-XDeriveAnyClass` is enabled, use that.
>
>

>
>
> (c) Otherwise, throw an error.
>
>


Step 2 is massively complicated since GHC tries to use `GeneralizedNewtypeDeriving` in certain special cases whenever it can to optimize the generated instances. In addition, the phrase "can be successfully used with `GeneralizedNewtypeDeriving`" must be invoked since it is possible for `GeneralizedNewtypeDeriving` on certain datatypes. For example, you cannot have a newtype-derived `Functor` instance for `newtype Compose f g a = Compose (f (g a))`, since the last type variable `a` cannot be eta-reduced.



Step 2.(c) deserves some explanation. As a rule, in the absence of an explicit `anyclass` keyword, GHC will never attempt to derive a "standard derivable class" using the `DeriveAnyClass` strategy, since it is guaranteed that doing so would not produce the instance you'd want. This also applies when derivable a standard derivable class for a newtype when both `-XGeneralizedNewtypeDeriving` and `-XDeriveAnyClass` are enabled, which is why we need the special case of 2.(c) to come before 2.(d) so that `-XDeriveAnyClass` doesn't kick in when it shouldn't. This fixes the problem originally reported in Trac [\#10598](https://gitlab.staging.haskell.org/ghc/ghc/issues/10598).



The phrase "standard derivable class" is a bit sloppy since not all such classes are in a Haskell standard—in fact, many require turning on language extensions to be "standard" derived! Due to a lack of a better phrase, and since the phrase itself appears in GHC error messages, it's used here.



This provides another reason to use `-XDerivingStrategies`: trying to memorize this algorithm is almost impossible!


### Interaction with Safe Haskell



Safe Haskell has some things to say about derived `Typeable` and `Generic` instances, so it's worth mentioning how `-XDerivingStrategies` fits into the picture.



GHC currently disallows manually implementing `Typeable` instances, and derived `Typeable` instances are ignored, as GHC automatically generates `Typeable` instances for all datatypes, typeclasses, and promoted data constructors. Similarly, GHC will ignore derived `Typeable` instances even if a deriving strategy is used.



GHC also disallows manually implementing `Generic` instances when `-XSafe` is enabled, so the only way to declare `Generic` instances in Safe Haskell is to use the `-XDeriveGeneric` extension. To preserve this property, it is forbidden to derive a `Generic` instance with a deriving strategy other than `bespoke`.


### What `-XDerivingStrategies` is not



`-XDerivingStrategies` is not intended to be a catch-all language extension that enables all of `-XDeriveFunctor`, `-XDeriveAnyClass`, `-XGeneralizedNewtypeDeriving`, `-XDeriveGeneric`, and all other exotic `deriving` language extensions. To see why consider the following code:


```
{-# LANGUAGE GeneralizedNewtypeDeriving #-}
newtype T = MkT S deriving (Foo, Bar)
```


This code compiles without issue, and uses `GeneralizedNewtypeDeriving` to derive `Foo` and `Bar` instances for `T`. But if you turn on `-XDerivingStrategies` as well, suddenly the above code will change in semantics: it will emit a warning about `GeneralizedNewtypeDeriving` and `DeriveAnyClass` both being on, and default to `DeriveAnyClass`! The intention of `-XDerivingStrategies` is to simply enable new syntactic forms that allow strictly more code to compile, and in particular, it is not intended to change the semantics of any existing code.



In addition, having `-XDerivingStrategies` imply `-XGeneralizedNewtypeDeriving` would have Safe Haskell repercussions, since one cannot currently use `-XSafe` in combination with `-XGeneralizedNewtypeDeriving` (see Trac [\#8827](https://gitlab.staging.haskell.org/ghc/ghc/issues/8827)).


### Alternative syntax



Several alternative syntaxes and keyword suggestions have been proposed in the original track ticket ([\#10598](https://gitlab.staging.haskell.org/ghc/ghc/issues/10598)) and on the ghc-devs mailing list ([
https://mail.haskell.org/pipermail/ghc-devs/2016-July/012442.html](https://mail.haskell.org/pipermail/ghc-devs/2016-July/012442.html)). Here is an overview of some previous ideas:


- Use pragmas instead of keywords. We could indicate the use of deriving strategies like so:

```
newtype T a = T a
  deriving          Show
  deriving {-# BESPOKE  #-} (Eq, Foldable)
  deriving {-# NEWTYPE  #-} Ord
  deriving {-# ANYCLASS #-} Read

deriving {-# BESPOKE #-} instance Functor T
```

>
>
> This has the advantage of being backwards compatible. On the other hand, several people objected to this idea on the basis that the presence of pragmas shouldn't affect the semantics of programs.
>
>

- Use type synonyms instead of keywords. We could have three builtin type syonyms:

```
type Bespoke  (a :: k)  = a
type Newtype  (a :: k)  = a
type AnyClass (a :: k) = a
```

>
>
> that we imbue with compiler magic to indicate the presence of a deriving strategy. For example:
>
>

```
newtype T a = T a deriving (Bespoke Eq, Newtype Ord, AnyClass Read, Show)
deriving instance Bespoke (Functor T)
```

>
>
> This is fairly backwards compatible (back to GHC 7.6), and would require absolutely no Template Haskell or parser changes. On the other hand, it requires making type synonyms behave magically, and it muddies up the actual class being derived, perhaps making it more confusing to look at.
>
>

- Instead of allowing multiple `deriving` clauses per datatype, one could indicate the presence of a deriving strategy by preceding every derived class with the appropriate keyword. For example:

```
newtype T a = T a
  deriving ( Show
           , bespoke Eq
           , bespoke Foldable
           , newtype Ord
           , anyclass Read
           )
```

>
>
> This is cleaner in some respects, as it more closely resembles an English-language description of how to derive all the instances that `T` needs (`Eq` via bespoke, `Ord` via newtype, `Read` via anyclass...). A downside is that this is much trickier (though likely not impossible) to parse, since all-lowercase words can be confused for type variables.
>
>

>
>
> Another factor to consider is the semantic noise that this suggestion brings. Unlike with multiple `deriving` clauses, this suggestion requires the use of a keyword next to *every* class. This can lead to more keystrokes than the multi-clause suggestion would when you derive several instances with the same deriving strategy. For instance, in the above example you need to type `bespoke` twice, whereas in the multi-clause proposal you need only type `bespoke` once. This can really add up when you derive loads of instances at a time, e.g.,
>
>

```
newtype T a = T a
  deriving ( newtype A
           , newtype B
           , newtype C
           , newtype D
           , newtype E
           , newtype F
           )
```

>
>
> This can be expressed much more succintly as:
>
>

```
newtype T a = T a
  deriving newtype (A, B, C, D, E, F)
```

>
>
> In addition, I (the proposal author, Ryan) would argue that it's tidier to put the strategy keyword outside of the parentheses, since it makes it clear that these keywords aren't modifying the type we're deriving, only the *means* by which we're deriving it.
>
>

>
>
> One could also argue that GHC should support both syntaxes, although it would combine the downsides of both for questionable gain.
>
>

- Previous alternative suggestions for the `bespoke` keyword were `builtin`, `magic`, `wiredin`, `standard`, `native`, `original`, and `specialized`. In particular, `builtin` is what I (Ryan) originally suggested, but it was poorly received since *all* deriving extensions are, to some extent, built-in to GHC.
- A previous alternative suggestion for the `anyclass` keyword was `default`, since it's already a keyword, and the connection to `-XDefaultSignatures` would be evocative of generic programming, which `-XDeriveAnyClass` is often used for. On the other hand, I (Ryan) felt it would be too easy to confuse with what `bespoke` accomplishes (i.e., the "default" GHC behavior when deriving an instance), so I proposed `anyclass` to make it very explicit what's going on there.