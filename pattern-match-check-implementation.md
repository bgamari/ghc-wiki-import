# Exhaustiveness/Redundancy Check Implementation



This page describes how the exhaustiveness and redundancy cheker is implemented
in GHC. If you are looking for a general description of the problem and an
overview of our approach see [PatternMatchCheck](pattern-match-check).



The most relevant modules are:


- `deSugar/PmExpr.hs`:   Expressions (`PmExpr`, `PmLit`), term constraints (`SimpleEq`, `ComplexEq`) and utilities for the check and the term oracle.
- `deSugar/TmOracle.hs`: The term oracle and utilities. Three (incremental) interfaces to the oracle are exported: `tmOracle`, `solveOneEq` and `extendSubst`.
- `deSugar/Check.hs`:    The main algorithm. Main exports are functions `checkSingle` (check a single binding -- let) and `checkMatches` (check a `MatchGroup` -- case expressions and functions).


We discuss each one separately below.


## The `PmExpr` Datatype and Friends



The module exports the following data types:


```
data PmExpr = PmExprVar   Name
            | PmExprCon   DataCon [PmExpr]
            | PmExprLit   PmLit
            | PmExprEq    PmExpr PmExpr  -- Syntactic equality
            | PmExprOther (HsExpr Id)    -- Note [PmExprOther in PmExpr]

data PmLit = PmSLit HsLit                                    -- simple
           | PmOLit Bool {- is it negated? -} (HsOverLit Id) -- overloaded

type SimpleEq  = (Id, PmExpr)
type ComplexEq = (PmExpr, PmExpr)
```


Type `PmExpr` represents Haskell expressions. Since the term oracle is minimal at the moment, only specific forms are supported
and the rest are wrapped in a `PmExprOther`. Note that `PmExprEq` is not function `(==)` but represents structural equality and
is used for non-overloaded literals. E.g. the constraint \*x is not equal to 5\* is represented as (False \~ (x \~ 5)):


```
PmExprEq (PmExprCon <False> []) (PmExprEq (PmExprVar <x>) (PmExprLit (PmSLit <5>)))
```


Type `PmLit` represents literals (both overloaded and non-overloaded). Literal
equality is defined with signature:


```
eqPmLit :: PmLit -> PmLit -> Bool
```


Strictly speaking, the result type should be `Maybe Bool`, since if two overloaded literals look different it does not mean they actually are. Hence
equality in this case is inconclusive, unless we unfold the `from`-function (like `fromInteger`). E.g. syntactic equality 5 \~ 6 is
not necessarily inconsistent, since it actually represents `fromInteger 5 ~ fromInteger 6` which depends on the implementation of
function `fromInteger`. On the other hand, `fromInteger 5 ~ fromInteger 5` is always true, since `fromInteger` is a pure function.
We could have treated overloaded literals as function applications (e.g. `fromInteger 5`) but since the term oracle cannot handle
function applications, we would get poor error messages for overloaded literals. Instead, we took this \*syntactic\* approach (which
looks a bit hackish though).



Nevertheless, we close our eyes to the truth above and simply return a `Bool`. This way, the warnings are
more comprehensible.



Term equalities take two forms: `SimpleEq` and `ComplexEq`. In the paper we generate only simple equalities,
yet this is not so performant. In many cases we would generate `{ x ~ e1, x ~ e2 }` (for a fresh variable `x`)
instead of the much more direct `e1 ~ e2`. Hence, the current implementation generates `ComplexEq`s. `SimpleEq`s
are still used for nested pattern matching (see below).


## The Term Oracle



As explained in the paper, the algorithm depends on external solvers for checking
the satisfiability of term and type constraints. For checking type constraints we
simply call GHC's existing inference engine (OutsideIn(X)), using a simple interface:


```
tyOracle :: Bag EvVar -> PmM Bool
```


For the satisfiability of term constraints, we have implemented a simple oracle
(solver). The term oracle lives in `deSugar/TmOracle.hs` and has the following
signature(s):


```
solveOneEq  :: TmState -> ComplexEq -> Maybe TmState
tmOracle    :: TmState -> [ComplexEq] -> Maybe TmState
extendSubst :: Id -> PmExpr -> TmState -> TmState
```


Function `solveOneEq` --as the name suggests-- solves a single term constraint (which is the most common case)
while `tmOracle` takes a list of them. They are both designed to be called incrementally, hence they both take
a term oracle state as argument and return the resulting one (if consistent) as well. Function `extendSubst` is
used when we generate a fresh variable (e.g. when checking against guards), where no actual solving takes place.
It is much more efficient than `solveOneEq`, but is unsafe, since it does not check the invariant, that the
variable does not already exist in the state.



The state of the oracle
`TmState` is represented as follows:


```
type PmVarEnv    = Map.Map Name PmExpr
type TmOracleEnv = (Bool, PmVarEnv)
type TmState     = ([ComplexEq], TmOracleEnv)
```


`TmState` contains the following components:


1. A list of complex constraints that cannot be solved until we have more information,
1. A boolean (`True` iff at least one unhandled constraint has appeared),
1. A substitution from variables to `PmExpr`s (actual result of solving).


By **unhandled** we mean constraints that involve an `PmExprOther`, for which we know that the oracle
cannot do anything. We could store these constraints instead of keeping just a boolean (would be better
if we were to print them for more precise warnings). This is not done though, so, for efficiency, we
just keep a boolean. But why store something in the first place? For the laziness check, we need to know
if a clause forces any of the arguments. Since we want to play on the safe side, a constraint we cannot
inspect can fail for all we know, so this boolean is used for the computation of the divergent set.


### Stategy of the Oracle



The strategy of the term oracle is quite simplistic and proceeds as follows (for a single `SimpleEq`):


- Apply existing substitution to the equality using `applySubstComplexEq :: PmVarEnv -> ComplexEq -> ComplexEq`.
- Try to simplify the constraint using `simplifyComplexEq :: ComplexEq -> ComplexEq`.
- Try to solve the simplified complex equality using `solveComplexEq :: TmState -> ComplexEq -> Maybe TmState`.
  If the existing substitution is extended during solving, we apply the extended substitution to the
  constraints that could not progress before and recurse.


Function `solveOneEq` implements the above:


```
solveOneEq :: TmState -> ComplexEq -> Maybe TmState
solveOneEq solver_env@(_,(_,env)) complex
  = solveComplexEq solver_env
  $ simplifyComplexEq
  $ applySubstComplexEq env complex

tmOracle :: TmState -> [ComplexEq] -> Maybe TmState
tmOracle tm_state eqs = foldlM solveOneEq tm_state eqs
```


**Note:** We have not implemented an OccursCheck, since it is not needed at the moment. If extended though,
the oracle will need an occurs check to ensure termination (e.g. if function applications are unfolded by
the oracle).


## The Actual Check



The check itself is implemented in module `deSugar/Check.hs`. Exported functions:


```
-- Issue warnings
dsPmWarn :: DynFlags -> DsMatchContext -> DsM PmResult -> DsM ()

-- Compute the covered/uncovered/divergent sets and
-- issue the warnings if the respective flags are enabled
checkSingle :: DynFlags -> DsMatchContext -> Id -> Pat Id -> DsM ()                      -- let
checkMatches :: DynFlags -> DsMatchContext -> [Id] -> [LMatch Id (LHsExpr Id)] -> DsM () -- case/functions

-- Nested pattern matching
genCaseTmCs1 :: Maybe (LHsExpr Id) -> [Id] -> Bag SimpleEq

genCaseTmCs2 :: Maybe (LHsExpr Id) -- Scrutinee
             -> [Pat Id]           -- LHS       (should have length 1)
             -> [Id]               -- MatchVars (should have length 1)
             -> DsM (Bag SimpleEq)
```

- `checkSingle` performs the check on a single pattern (let bindings)
- `checkMatches` performs the check on a list of matches (case expressions / function bindings)
- `genCaseTmCs1` generates term constraints for nested pattern matching (see below)
- `genCaseTmCs2` generates term constraints for nested pattern matching (see below)


Before explaining what each function does, we first discuss the types used.


### The `PmPat` datatype and friends



The `PmPat` data type is defined in `deSugar/Check.hs` as:


```
data PatTy = PAT | VA -- Used only as a kind, to index PmPat

data PmPat :: PatTy -> * where
  PmCon  :: { pm_con_con     :: DataCon
            , pm_con_arg_tys :: [Type]
            , pm_con_tvs     :: [TyVar]
            , pm_con_dicts   :: [EvVar]
            , pm_con_args    :: [PmPat t] } -> PmPat t
            -- For PmCon arguments' meaning see @ConPatOut@ in hsSyn/HsPat.hs
  PmVar  :: { pm_var_id   :: Id    } -> PmPat t
  PmLit  :: { pm_lit_lit  :: PmLit } -> PmPat t -- See Note [Literals in PmPat]
  PmNLit :: { pm_lit_id   :: Id
            , pm_lit_not  :: [PmLit] } -> PmPat VA
  PmGrd  :: { pm_grd_pv   :: PatVec
            , pm_grd_expr :: PmExpr  } -> PmPat PAT
```


Literal patterns are not essential for the algorithm to work, but it is
more efficient to treat them more like constructors by matching against them eagerly than
generating equality constraints to feed the term oracle with (as we do in the paper). Additionally,
we use negative patterns (constructor `PmNLit`), for efficiency also. `PmCon` contains several
fields, effectively copying constructor `ConPatOut` of type `Pat` (defined in `hsSyn/HsPat.hs`).
Since the algorithm runs post-typechecking, we have a lot of information available for the
treatment of GADTs.


### Value Abstractions & Patterns



Apart from the literal/negative literal patterns, both value abstractions and patterns look exactly
like in the paper, and are both implemented by the `PmPat` type:


```
type Pattern = PmPat PAT -- ^ Patterns
type ValAbs  = PmPat VA  -- ^ Value Abstractions
```


Similarly, a pattern vector is just a list of patterns, while a value vector abstraction looks
like the paper, that is, it is a list of value abstractions, accompanied by a set of constraints `Delta`:


```
type PatVec = [Pattern]             -- ^ Pattern Vectors
data ValVec = ValVec [ValAbs] Delta -- ^ Value Vector Abstractions

data Delta = MkDelta { delta_ty_cs :: Bag EvVar
                     , delta_tm_cs :: TmState }
```


A big difference from the theory is that instead of storing term constraints, we store in `Delta` the whole
term oracle state, that is, a much more efficient representation of the term constraints. This allows us to
have significantly better performance, especially since we have an incremental interface for the term oracle.


### Value Set Abstractions (Covered, Uncovered and Divergent Sets)



Value set abstractions are just represented as a list of value vector abstractions:


```
type ValSetAbs = [ValVec]  -- ^ Value Set Abstractions
type Uncovered = ValSetAbs
```


**NOTE**: We have attempted to use a nicer representation of value set abstractions as prefix trees but
we faced some performance issues. Hence, we created the task [\#11528](https://gitlab.staging.haskell.org/ghc/ghc/issues/11528) to further investigate whether such
an approach is achievable.



Note that the only set we store is the uncovered set: For both the divergent and the covered set, we are only
interested in whether they are empty or not. Hence, instead of creating the sets and checking whether they are
empty or not afterwards, we check on the fly and use just a boolean for each.


### General Strategy



The algorithm process as follows:


- Translate a clause (which contains `Pat`s, defined in `hsSyn/HsPat.hs`) to `PatVec`s (`translatePat`
  is the workhorse):

  ```
  translatePat    :: FamInstEnvs -> Pat Id -> PmM PatVec
  translateGuards :: FamInstEnvs -> [GuardStmt Id] -> PmM PatVec
  translateMatch  :: FamInstEnvs -> LMatch Id (LHsExpr Id) -> PmM (PatVec,[PatVec])
  ```
- Call function `pmcheck` on the transformed clause:

  ```
  pmcheck       ::            PatVec -> [PatVec]           -> ValVec -> PmM Triple
  pmcheckHd     :: Pattern -> PatVec -> [PatVec] -> ValAbs -> ValVec -> PmM Triple
  pmcheckGuards ::                      [PatVec]           -> ValVec -> PmM Triple

  type Triple = (Bool, Uncovered, Bool)
  ```


Note that a processed clause has type `(PatVec,[PatVec])`. Why? Because each clause may be accompanied by a list
of guard-vectors. The formalization indicates that `patVectProc` gets a clause but in order to achieve this in
the presence of more than one guard-vector, we should duplicate the pattern vector (and possibly alpha-rename
it). That is:


```wiki
(vec, [gv1, gv2, .., gvN]) == [ vec ++ gv1
                              , vec ++ gv2
                              ...
                              , vec ++ gvN
```


Since this is really bad for performance (significantly increases the number of clauses), we took a different
approach: Process the guards separately using `pmcheckGuards` and plug the results at the end of the respective
clause. Yet, this comes with a price: We are not able to issue warnings per-guard but only per-clause. This was
the case already in GHC though (see ticket [\#8494](https://gitlab.staging.haskell.org/ghc/ghc/issues/8494)).



All three `pmcheck`-functions compute (as the signatures suggest) coverage, exhaustiveness and divergence at
once. `pmcheck` is the main interface that takes a pattern vector, the guard vectors and the uncovered set and
returns all three results. It basically handles the guard case (see paper) and if both the value vector
abstraction and the pattern vector are in head-tail form, calls `pmcheckHd` to handle the rest of the cases.
When both the value vector abstraction and the pattern vector are empty, `pmcheckGuards` is called, to handle
the guards.



Since GHC is a production compiler and the uncovered set can grow exponentially, the recursive calls had to be
bounded. Hence, three variants are used in the recursive positions: `pmcheckI`, `pmcheckHdI` and `pmcheckGuardsI`
(`I` comes from "increment"). All three variants simply call the respective function, after increasing a counter
of the iterations elapsed. For example, `pmcheckI` is defined as follows:


```
pmcheckI :: PatVec -> [PatVec] -> ValVec -> PmM Triple
pmcheckI ps guards vva = incrCheckPmIterDs >> pmcheck ps guards vva
```


By default, the number of iterations for bailing is 10000000 (none of the tests we have tried seems to exceed that
yet) but it can be set with: `-fmax-pmcheck-iterations=n`. Function `warnPmIters` in `deSugar/Check.hs` issues a
warning if the limit is exceeded.


### Nested Pattern Matching



Since we want to handle nested pattern matching too, two more functions are exported by `deSugar/Check.hs`
which are used to generate constraints for case expressions:


```
genCaseTmCs1 :: Maybe (LHsExpr Id) -> [Id] -> Bag SimpleEq
genCaseTmCs2 :: Maybe (LHsExpr Id) -- Scrutinee
             -> [Pat Id]           -- LHS       (should have length 1)
             -> [Id]               -- MatchVars (should have length 1)
             -> DsM (Bag SimpleEq)
```

- Function `genCaseTmCs1` takes a scrutinee of a case expression `e` and a variable `x` and generates the
  term equality `x ~ e`. Variable `x` is the one used in the initially uncovered set so the generated constraint
  records the equality of the scrutinee with all clauses.
- Function `genCaseTmCs2` takes the scrutinee `e`, an LHS `p`, and the initially uncovered variable `x` and
  generates the set of constraints `{ x ~ e, x ~ to_expr(p) }`. How is this useful? Consider the following
  example:

  ```
  f :: Bool -> ()
  f y = case y of
          x@True ->                 -- genCaseTmCs2 generates: y ~ True, y ~ x
                    case x of
                      True  -> ()
                      False -> ()   -- genCaseTmCs1 generates: x ~ False (*)
          False  -> ()
  ```


This way we can detect that (\*) is redundant. Note that the **right-thing-to-do** would be to compute the whole
covered set for clause `x@True`, and for every value vector abstraction in the set check the nested pattern
matching. Yet, this is too expensive so this **hack** saves us the trouble and quite cheaply.


### Other Approximations (and hacks?)



The algorithm described in the paper is too expressive (especially when it comes to guards) so implementing it
in GHC required several approximations and simplifications:


1. Function `translateGuards` drops unhandled guards. That is, if there are guards involving `PmExprOther` that
  will make the algorithm branch but the oracle will not be able to reason about, it replaces them with a
  single may-fail guard. This way, we record the possibility of failure, without generating unreasonably big
  value set abstractions.
1. As-patterns are translated in reverse: An as-pattern `x@p` should be formally translated to a variable
  pattern `x` and a guard pattern `x <- translate p`. This has a big disadvantage though: pattern matching
  is transferred to the guard pattern so resolution is postponed until the term oracle. Consider the following
  example:

  ```
  f :: Bool -> ()
  f x@True  = ()
  f y@False = ()
  ```

  The uncovered set with the above translation is `{ x |> {x ~ False, x ~ y, y ~ True} }`. Obviously this is
  empty but we should call the term oracle to detect the inconsistency and deduce that the pattern match is
  exhaustive. Instead, we translate as-patterns `x@p` as follows: `p (x <- coercePmPat p)`. Function
  `coercePmPat` drops all the guard patterns from `p` so it can be transformed to an expression. Even though
  this translation is a bit unintuitive, it is efficient and equivalent to the formal one:

  - Pattern `p` contains all the guards so no information is lost
  - Guard pattern `(x <- coercePmPat p)` is **bidirectional** in the sense that an equality
    `x ~ coercePmPat p` will ultimately be generated, so no information is lost.

  In summary, the following will be generated:

  ```
  f :: Bool -> ()
  f True  (x <- True)  = () -- uncovered: { False |> { x ~ True } }
  f False (y <- False) = () -- uncovered: {}
  ```

1. In general, when a guard is not going to contribute, during translation we transform it to a `can fail`
  pattern:

  ```
  -- | A fake guard pattern (True <- _) used to represent cases we cannot handle
  fake_pat :: Pattern
  fake_pat = PmGrd { pm_grd_pv   = [truePattern]
                   , pm_grd_expr = PmExprOther EWildPat }
  ```

  Later when we check (with `pmcheck`), in case we reach a guard, we short-circuit if it is a "fake" one.
  This trick saves a lot of effort in practice (in terms of both memory consumption and performance):

  ```
  pmcheck (p@(PmGrd pv e) : ps) guards vva@(ValVec vas delta)
    | isFakeGuard pv e = forces . mkCons vva <$> pmcheckI ps guards vva
    | otherwise = ...
  ```