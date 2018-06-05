# Handling of Source Locations in Trees that Grow


## Problem



The current design of [
TTG HsSyn AST](https://ghc.haskell.org/trac/ghc/wiki/ImplementingTreesThatGrow/TreesThatGrowGuidance) in GHC stores source locations for terms of a datatype `Exp` in a separate wrapper datatype `LExp` which is mutually recursive with `Exp` such that every recursive reference to `Exp` is done **indirectly**, via a reference to the wrapper datatype `LExp` (see the example code below). We refer to this style of storing source locations as the ping-pong style.



Besides the indirection and the resulting complications of the ping-pong style, there are two key problems with it: 


1. It bakes-in the source locations in the base TTG AST, forcing all instances to store source locations, even if they don't need them.
  For example, TH AST does not carry source locations. 

1. It results in a form of conceptual redundancy: source locations are tree decorations and they belong in the extension points.
  (see [
  TTG Guidance](https://ghc.haskell.org/trac/ghc/wiki/ImplementingTreesThatGrow/TreesThatGrowGuidance))

### Example



For example, here is a simple [
TTG](https://ghc.haskell.org/trac/ghc/wiki/ImplementingTreesThatGrow/TreesThatGrowGuidance) representation of lambda expressions in the ping-pong style.


```wiki
{-# LANGUAGE TypeFamilies
           , ConstraintKinds
#-}
module Original where

import GHC.Exts(Constraint)
import Data.Void

-- ...

data RdrName
-- = the definition of RdrName

data SrcSpan
-- = the definition of SrcSpan

data Located a = L SrcSpan a

-- ----------------------------------------------
-- TTG Base AST
-- ----------------------------------------------
type LExp x = Located (Exp x)

data Exp x
  = Var (XVar x) (XId x)
  | Lam (XLam x) (XId x)  (LExp x)
  | App (XApp x) (LExp x) (LExp x)
  | Par (XPar x) (LExp x)
  | New (XNew x)

type family XVar x
type family XLam x
type family XApp x
type family XPar x
type family XNew x

type family XId  x

type ForallX (p :: * -> Constraint) x
  = ( p (XVar x)
    , p (XLam x)
    , p (XApp x)
    , p (XPar x)
    , p (XNew x)
    )

-- ----------------------------------------------
-- AST Ps (parsing phase)
-- ----------------------------------------------

data Ps

type ExpPs  = Exp  Ps
type LExpPs = LExp Ps

type instance XVar Ps = ()
type instance XLam Ps = ()
type instance XApp Ps = ()
type instance XPar Ps = ()
type instance XNew Ps = Void

type instance XId  Ps = RdrName

-- ----------------------------------------------
-- Example Function (e.g., in pretty printing)
-- ----------------------------------------------

par :: LExp Ps -> LExp Ps
par l@(L sp m) = L sp (Par () l)
```

## Solutions



The key solution is to move source locations to the extension points, remove the indirection (e.g., the wrapper datatype `LExp`) altogether, and update the related code (e.g., functions over `Exp`) accordingly. 
There are a couple of ways to implement such a solution:


1. Using a typeclass to set/get source locations

1. We can nest extension typefamilies to be able to say that all constructors have the same uniform decorations (e.g., `SrcSpan`) beside their specific ones. This is just for convenience as `ForallX*` constraint quantifications can simulate the same (see the code for solution A).

1. We can extend (using TTG) each datatype to add a wrapper constructor like the current `Located`.

1. The API Annotations are similar to the `SrcSpan`, in that they are additional decorations, and also currently appear wherever there is a `SrcSpan`.

1. We also currently have sections of AST without source locations, such as those generated when converting TH AST to hsSyn AST, or for GHC derived code.


We can perhaps deal with these by either defining an additional pass, so


```
data Pass = Parsed | Renamed | Typechecked | Generated
         deriving (Data)
```


or by making the extra information status dependent on an additional parameter, so


```
data GhcPass (l :: Location) (c :: Pass)
deriving instance Eq (GhcPass c)
deriving instance (Typeable l,Typeable c) => Data (GhcPass l c)

data Pass = Parsed | Renamed | Typechecked
         deriving (Data)

data Location = Located | UnLocated
```


Thanks to Zubin Duggal for bringing the unlocated problem up on IRC.
 


1. TODO (add your suggestions)

### Example (Solution A)



In the code below, as compared to the one above, we have the following key changes:


- `LExp` is replaced with `Exp`
- field extensions are set to have a `SrcSpan` (instead of `()`)
- a setter/getter typeclass `HasSpan` (and instances) is introduced
- a pattern synonym for `L` is introduced using the typeclass

```
{-# LANGUAGE TypeFamilies
           , ConstraintKinds
           , FlexibleInstances
           , FlexibleContexts
           , UndecidableInstances
           , PatternSynonyms
           , ViewPatterns
#-}
module New where

import GHC.Exts(Constraint)
import Data.Void

-- ...

data RdrName
-- = the definition of RdrName

data SrcSpan
-- = the definition of SrcSpan

-- ----------------------------------------------
-- TTG Base AST
-- ----------------------------------------------

data Exp x
  = Var (XVar x) (XId x)
  | Lam (XLam x) (XId x) (Exp x)
  | App (XApp x) (Exp x) (Exp x)
  | Par (XPar x) (Exp x)
  | New (XNew x)

type family XVar x
type family XLam x
type family XApp x
type family XPar x
type family XNew x

type family XId  x

type ForallX (p :: * -> Constraint) x
  = ( p (XVar x)
    , p (XLam x)
    , p (XApp x)
    , p (XPar x)
    , p (XNew x)
    )

-- ----------------------------------------------
-- AST Ps
-- ----------------------------------------------

data Ps

type ExpPs  = Exp Ps

type instance XVar Ps = SrcSpan
type instance XLam Ps = SrcSpan
type instance XApp Ps = SrcSpan
type instance XPar Ps = SrcSpan
type instance XNew Ps = Void

type instance XId  Ps = RdrName

-- ----------------------------------------------
-- HasSpan Typeclass
-- ----------------------------------------------

class HasSpan a where
  getSpan :: a -> SrcSpan
  setSpan :: a -> SrcSpan -> a

instance HasSpan SrcSpan where
  getSpan   = id
  setSpan _ = id

instance HasSpan Void where
  getSpan x   = absurd x
  setSpan x _ = absurd x

instance ForallX HasSpan x => HasSpan (Exp x) where
  getSpan (Var ex _)      = getSpan ex
  getSpan (Lam ex _ _)    = getSpan ex
  getSpan (App ex _ _)    = getSpan ex
  getSpan (New ex)        = getSpan ex

  setSpan (Var ex x)   sp = Var (setSpan ex sp) x
  setSpan (Lam ex x n) sp = Lam (setSpan ex sp) x n
  setSpan (App ex l m) sp = App (setSpan ex sp) l m
  setSpan (New ex)     sp = New (setSpan ex sp)

getSpan' :: HasSpan a => a -> (SrcSpan , a)
getSpan' m = (getSpan m , m)

pattern L :: HasSpan a => SrcSpan -> a -> a
pattern L s m <- (getSpan' -> (s , m))
  where
        L s m =  setSpan m s

-- ----------------------------------------------
-- Example Function
-- ----------------------------------------------

par :: Exp Ps -> Exp Ps
par l@(L sp m) = Par sp l
  -- or,
  --           = L sp (Par noLoc l)
```

### Include API Annotations, Solution D



The API Annotations can be accommodated via a straightforward extension of the type class approach, by defining


```
data Extra = Extra SrcSpan [(SrcSpan,AnnKeywordId)]

class HasExtra a where
  getSpan :: a -> SrcSpan
  setSpan :: a -> SrcSpan -> a

  getApiAnns :: a -> [(SrcSpan,AnnKeywordId)]
  setApiAnns :: a -> [(SrcSpan,AnnKeywordId)] -> a
```