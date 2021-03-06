# Kind-polymorphic `Typeable`in GHC 7.8



The page describes an improved implementation of the `Typeable` class, using polymorphic kinds, available from GHC 7.8.  Technically it is straightforward, but it represents a non-backward-compatible change to a widely used library, so we had to make a plan for the transition.



Relevant tickets we fixed: [\#5391](https://gitlab.staging.haskell.org/ghc/ghc/issues/5391), [\#5863](https://gitlab.staging.haskell.org/ghc/ghc/issues/5863).


## The `Typeable` class before 7.8



Before 7.8, the `Typeable` class was as follows:


```wiki
class Typeable (a :: *) where
  typeOf :: a -> TypeRep
```


Because it was mono-kinded we also had


```wiki
class Typeable1 (f :: *->*) where
  typeOf1 :: f a -> TypeRep
```


and so on up to `Typeable7`.  It was a mess, and we couldn't make `Typeable` at all for
type constructors with higher kinds like


```wiki
  Foo :: (* -> *) -> *
```


See [\#5391](https://gitlab.staging.haskell.org/ghc/ghc/issues/5391).


## The new `Typeable` class, in GHC 7.8



Having polymorphic kinds lets us say this:


```wiki
data Proxy t = Proxy

class Typeable t where
  typeRep :: proxy t -> TypeRep
```


Notice that


- `Typeable` has a polymorphic kind:

  ```wiki
    Typeable :: forall k. k -> Constraint
  ```

- The method is called `typeRep` rather than `typeOf`

- One reason for the name change is that the argument is not a value of the type `t`, but a value of type `(proxy t)`.  We have to do this because `t` may have any kind, so we can't say 

  ```wiki
    typeOf :: t -> TypeRep
  ```

- You can instantiate `proxy` to whatever you want; one common choice is the poly-kinded `Proxy` datatype:

  ```wiki
    Proxy:: forall k. k -> *
  ```


Now the base library code can have kind-specific instances:


```wiki
instance Typeable Int where typeRep _ = ...
instance Typeable []  where typeRep _ = ...
instance (Typeable a, Typeable b) => Typeable (a b) where
  typeRep _ = ...
```


A use of `deriving( Typeable )` for a type constructor `T` always generates


```wiki
instance Typable T where typeRep _ = ....
```


i.e. an instance of `T` itself, not applied to anything.


## How to make your code compile again



If you have code involving `Typeable` that fails to compile with 7.8, it might be due to the changes described above. Here's a few things to keep in mind in order to make your code compile again:
  


- Users can no longer giving manual instances of `Typeable`; they must be derived.

- Manual instances were often written for datatypes with non kind-`*` arguments. These can now be derived without problems. So if you had, for example:

  ```wiki
  data Fix f = In (f (Fix f))
  instance (Typeable1 f) => Typeable (Fix f) where typeOf = ...
  ```

  you can now simply attach `deriving Typeable` to `Fix`.

- You can still use `typeOf1..7`; they are now just (deprecated) type-specific versions of `typeRep`. But keep in mind that they are no longer methods of a class, as the classes `Typeable1..7` no longer exist.

- You can still use `Typeable1..7`; they are now just (deprecated) type synonyms for `Typeable`, fixing the kind of their argument. But keep in mind that they are no longer classes, just type synonyms.

- If all else fails, you could just try replacing your `import Data.Typeable` with `import Data.OldTypeable`. But keep in mind that `OldTypeable` is distinct, and incompatible with the new `Typeable`.

- If you want code that compiles with multiple versions of GHC, you should use CPP. The [
  tagged package on Hackage](http://hackage.haskell.org/package/tagged) is a good example of how to achieve this.

## A change-over plan



**In GHC 7.8:**


- Rename `Data.Typeable` to `Data.OldTypeable` and deprecate the whole module.

- Define a new library `Data.Typeable` with the new definitions in them.

- Include in `Data.Typeable` old methods for backward compatibility, but deprecate them:

  ```wiki
  typeOf :: forall a. Typeable a => a -> TypeRep
  typeOf _ = typeRep (Proxy :: Proxy a)

  typeOf1 :: forall t (a :: *). Typeable t => t a -> TypeRep
  typeOf1 _ = typeRep (Proxy :: Proxy t)

  type Typeable1 (a :: * -> *)                               = Typeable a
  type Typeable2 (a :: * -> * -> *)                          = Typeable a
  ```

- Make `deriving( Typeable )` work with whatever `Typeable` class is in scope.  So what it does will be determined by whether you say `import Data.Typeable` or `import Data.OldTypeable`.


**In GHC  7.10:**


- Remove `Data.OldTypeable`

## Aside



Open question: what are the corresponding changes to `Data.Data`?  See [\#4896](https://gitlab.staging.haskell.org/ghc/ghc/issues/4896).


