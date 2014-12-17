CONVERSION ERROR

Original source:

```trac

= Exhaustiveness/Redundancy check =

As stated in #595, GHC's overlapping/non-exhaustive pattern checking is old and
crufty and misbehaves with several GHC's extensions, notably GADTs. In this page
we describe the problem and the algorithm we are currently implementing.

See also
 * Our (on-going) work on the formalisation of the algorithm [url-here Algorithm].
 * The paper on which the previous approach were based [http://moscova.inria.fr/~maranget/papers/lazy-pats-derniere.ps.gz Two techniques for compiling lazy pattern matching]
 * [wiki:PatternMatchCheckImplementation] talks about the implementation in GHC.

= The main problem we wish to solve =

Since GHC's exhaustiveness/redundancy checker is outdated, it does not take into
account constraints introduced by GADT matches when reporting warnings. This is
illustrated in the following example (#3927):

{{{
data T a where
  T1 :: T Int
  T2 :: T Bool

f :: T a -> T a -> Bool
f T1 T1 = True
f T2 T2 = False
}}}

Even though the above definition for `f` is exhaustive, we get a warning of the
form:
{{{
    Pattern match(es) are non-exhaustive
    In an equation for `f':
        Patterns not matched:
            T1 T2
            T2 T1
}}}

Obviously, both pattern vectors issued as not matched, are ill-typed, because
they both generate the inconsistent constraint `Int ~ Bool`. This becomes more
clear if we rewrite the definition of `T` in the equivalent form:
{{{
data T a where
  T1 :: forall a. (a ~ Int)  => T a
  T2 :: forall a. (a ~ Bool) => T a
}}}

Additionally, if we add one more branch to `f`:
{{{
f :: T a -> T a -> Bool
f T1 T1 = True
f T2 T2 = False
f _  _  = undefined -- inaccessible
}}}

we get no warning about the redundancy of the last clause.

= General approach =

Note that improving the redundancy check is quite more challenging than the
exhaustiveness check. For exhaustiveness it is sufficient to collect all potentially
missing vectors using the previous syntax-based approach and then filter out the
ill-typed. Nevertheless, for redundancy we need to compute the cases that are
covered by every clause and then filter out the ill-typed. The difficulty lies in
the fact that what the last branch of `f` covers depends on what remains uncovered
by the above two clauses. This indicates that for the redundancy check we need an
incremental way of computing uncovered vectors.

= The solution =

Until now, the algorithm used by GHC was based on a technique originally introduced
for compilation of pattern matching in decision trees (see paper above). Hence, it
used a column-based approach to traverse the pattern matrix, which cannot be used
incrementally as we descibed above.

Instead, we traverse the pattern matrix line-by-line. The general approach is the
following: We start with everything considered as missing and then, for every clause
we compute:
 * Which cases it covers
 * If it forces the evaluation of arguments (see Laziness below)
 * Which cases are left unhandled

For example, for function `f` above we have:
 * initial_missing = `[[_ _]]`
{{{
f T1 T1 = True -- first clause
}}}
 * Covers `[[T1 T1]]`
 * Forces the evaluation of the 1st argument
 * If 1st argument is `T1` forces the evaluation of the 2nd argument
 * Remain uncovered `[[T2 _], [T1 T2]]`
{{{
f T2 T2 = False -- second clause
}}}
 * Covers `[[T2 T2]]`
 * If 1st argument is `T2` forces the evaluation of the 2nd argument
 * Remain uncovered `[[T2 T1], [T1 T2]]`
{{{
f _  _  = undefined -- third clause (inaccessible)
}}}
 * Covers: `[[T2 T1], [T1 T2]]`
 * Doesn't force anything
 * Remain uncovered: `[]`

Now we can easily check both covered and uncovered cases and filter out the
ill-typed, before deciding if the match is exhaustive and which clauses are
useful.

= Laziness =

Even without GADTs, the previous algorithm was not exactly laziness-aware. For
example, for function `g` below
{{{
g :: Bool -> Bool -> Bool
g _    True = True
g True True = True
g _    _    = False
}}}

We would get a warning
{{{
    Pattern match(es) are overlapped
    In an equation for `g': g True True = ...
}}}

Yet, this is not correct. The second clause may be totally overlapped by the
first clause but it is not actually redundant. If we call `g` we get:
{{{
ghci> g undefined False
*** Exception: Prelude.undefined
}}}

because the second clause forces the evaluation of the first argument. Yet, if
we remove it we get:
{{{
ghci> g undefined False
False
}}}

That is, we changed the semantics of `g`.
```