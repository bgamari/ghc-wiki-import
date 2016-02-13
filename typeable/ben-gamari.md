# A plan for type-indexed type representations


## `Data.Typeable`



The user-visible interface of `Data.Typeable` will look like this,


```
-- The user-facing interface
module Data.Typeable where

class Typeable (a :: k)

-- This is how we get the representation for a type
typeRep :: forall (a :: k). Typeable a => TypeRep a 

-- This is merely a record of some metadata about a type constructor.
-- One of these is produced for every type defined in a module during its
-- compilation.
--
-- This should also carry a fingerprint; to address #7897 this fingerprint
-- should hash not only the name of the tycon, but also the structure of its
-- data constructors
data TyCon

tyConPackage :: TyCon -> String
tyConModule :: TyCon -> String
tyConName :: TyCon -> String

-- A runtime type representation with O(1) access to a fingerprint.
data TypeRep (a :: k)

instance Show (TypeRep a)

-- Since TypeRep is indexed by its type and must be a singleton we can trivially
-- provide these
instance Eq (TTypeRep a)  where (==) _ _    = True
instance Ord (TTypeRep a) where compare _ _ = EQ

-- While TypeRep is abstract, we can pattern match against it:
pattern TRApp :: forall k2 (fun :: k2). ()
              => forall k1 (a :: k1 -> k2) (b :: k1). (fun ~ a b)
              => TypeRep a -> TypeRep b -> TypeRep fun

pattern TRCon :: forall k (a :: k). TyCon -> TypeRep a

-- decompose functions
pattern TRFun :: forall fun. ()
              => forall arg res. (fun ~ (arg -> res))
              => TypeRep arg
              -> TypeRep res
              -> TypeRep fun

-- We can also request the kind of a type
tTypeRepKind :: TypeRep (a :: k) -> TypeRep k

-- and compare types
eqTypeRep  :: forall k (a :: k) (b :: k).
              TypeRep a -> TypeRep b -> Maybe (a :~: b)
eqTypeRep' :: forall k1 k2 (a :: k1) (b :: k2).
              TypeRep a -> TypeRep b -> Maybe (a :~~: b)

-- it can also be useful to quantify over the type such that we can, e.g.,
-- index a map on a type
data TypeRepX where
    TypeRepX :: TypeRep a -> TypeRepX

-- these have some useful instances
instance Eq TypeRepX
instance Ord TypeRepX
instance Show TypeRepX

-- A `TypeRep a` gives rise to a `Typeable a` instance without loss of
-- confluence.
withTypeable :: TypeRep a -> (Typeable a => b) -> b
withTypeable = undefined

-- We can also allow the user to build up his own applications
mkTrApp :: forall k1 k2 (a :: k1 -> k2) (b :: k1).
           TTypeRep (a :: k1 -> k2)
        -> TTypeRep (b :: k1)
        -> TTypeRep (a b)

-- However, we can't (easily) allow instantiation of TyCons since we have
-- no way of producing the kind of the resulting type...
--mkTrCon :: forall k (a :: k). TyCon -> [TypeRep] -> TTypeRep a
```

## The representation serialization problem



Serialization of type representations is a bit tricky in this new world,


```

```

## `Data.Dynamic`



`Dynamic` doesn't really change,


```
module Data.Dynamic where

-- Dynamic itself no longer needs to be abstract
data Dynamic where
    Dynamic :: TypeRep a -> a -> Dynamic

-- Construction
toDynR :: TypeRep a -> a -> Dynamic
toDyn  :: Typeable a => a -> Dynamic

-- Elimination
fromDynamicR :: TypeRep a -> Dynamic -> Maybe a
fromDynamic  :: Typeable a => Dynamic -> Maybe a

-- 
fromDynR :: TypeRep a -> Dynamic -> a -> a
fromDyn  :: Typeable a => Dynamic -> a -> a

-- Application
dynApp   :: Dynamic -> Dynamic -> Dynamic  -- Existing function; calls error on failure
                                           -- I think this should be deprecated
dynApply :: Dynamic -> Dynamic -> Maybe Dynamic
```


Ben Pierce also
[
https://ghc.haskell.org/trac/ghc/wiki/TypeableT\#Data.Dynamic\|suggested](https://ghc.haskell.org/trac/ghc/wiki/TypeableT#Data.Dynamic|suggested) this
variant of `Dynamic`, which models a value of dynamic type "inside" of a known
functor. He p


```
data SDynamic s where
    SDynamic :: TypeRep a -> s a -> SDynamic s

toSDynR :: TypeRep a -> s a -> SDynamic s
toSDyn :: Typeable a => s a -> SDynamic s
fromSDynamicR :: TypeRep a -> SDynamic s -> Maybe (s a)
fromSDynamic :: Typeable a => SDynamic s -> Maybe (s a)
fromSDynR :: TypeRep a -> SDynamic s -> s a -> s a
fromSDyn :: Typeable a => SDynamic s -> s a -> s a
```