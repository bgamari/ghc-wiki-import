CONVERSION ERROR

Original source:

```trac
= Allow rewrite rules to examine term context =

As of GHC 7.10 rewrite rules, even built-in ones, cannot inspect the context of the term they are considering in deciding whether they will rewrite it. Context can be an important hint in determining whether a given term will benefit from a rewrite.

Consider, for example, the `litEq` built-in rule (see Bug #9661 for further motivation). This rewrites expressions such as `n ==# 3` into case analyses of the form,
{{{#!hs
case n of
  3  -> True
  _  -> False
}}}

While this is usually a good thing, when applied indiscriminantly it will interfere with the user's attempt at using unboxed booleans. For instance,
the user might write,
{{{#!hs
f :: Int -> String
f (I# n) = case isTrue# pred of
        True  -> "That's Numberwang!"
        False -> "Oh dear." 
  where
    pred = (n ==# 3#) `orI#` (n ==# 42#) `orI#` (n ==# 78#) `orI#` (n ==# 90#)
}}}

Unfortunately in this case `litEq` will rewrite the user's carefully written unboxed expression as a case expression,
{{{#!hs
f :: Int -> String
f =
  \ ds_dPI ->
    case ds_dPI of _ { I# n_an9 ->
    case n_an9 of _ {
      __DEFAULT -> lvl_r3yJ;
      3 -> lvl1_r3yK;
      42 -> lvl1_r3yK;
      78 -> lvl1_r3yK;
      90 -> lvl1_r3yK
    }
    }
}}}

which produces the same branch-y assembler as the user was likely trying to avoid in the first place.

For this reason, we'd like to ensure that `litEq` (and similar built-in rewrite rules) does not rewrite unless the term is directly scrutinized by a case expression, ensuring that the user's careful work is preserved (open question: might this give up desirable optimizations?).

== Implementation ==

Built-in rewrite rules are currently encoded as a `RuleFun`,
{{{#!hs
type RuleFun = DynFlags
            -> InScopeEnv       -- ^ The scope within which the call is embedded
            -> Id               -- ^ The name of the called function
            -> [CoreExpr]       -- ^ The arguments of the call
            -> Maybe CoreExpr   -- ^ The resulting rewrite if appropriate
}}}

The simplifier currently encodes the context surrounding the term being simplified in a zipper-like fashion in the `SimplCont` type. We want to allow the `RuleFun` access to the `SimplCont` so that it can account for context when deciding whether to rewrite,

{{{#!hs
type RuleFun = DynFlags
            -> InScopeEnv       -- ^ The scope within which the call is embedded
            -> SimplCont        -- ^ The context surrounding the call
            -> Id               -- ^ The name of the called function
            -> [CoreExpr]       -- ^ The arguments of the call
            -> Maybe CoreExpr   -- ^ The resulting rewrite if appropriate
}}}

As the simplifier already keeps track of the context when evaluating rules, the change is mostly straightforward.


=== Rule checker issues ===

The primary difficulty is posed by the `ruleCheck` diagnostics functionality. This code is intended to provide the user with human-readable feedback on why rewrite rules matching a user-specified predicate do not fire on a term-by-term basis. The implementation is currently quite simple: it traverses the program examining function applications, looking for rules which both pertain to the applied function and match a user-specified predicate. It then produces a human-readable message in the event that the rule would not fire. Unfortunately, to evaluate whether the rule will fire we actually need to try calling the `RuleFun`, which now requires having a `SimplCont`.

Unfortunately the rule checker knows nothing about `SimplCont` and teaching it to track context would introduce a substantial amount of complexity. As far as I can tell there are three ways to address this,

 1. Pass a dummy `SimplCont` (probably a `Stop`) to the `RuleFun`. This
    is by far the easiest option but will result in discrepancies
    between the rule check and the rules actually fired by simplifier.

 2. Replicate the simplifier's logic to produce a `SimplCont` in the
    rule check. This seems like it will result in a great deal of
    unnecessary (and non-trivial) code duplication.

 3. Fold the rule check into the simplifier. It seems like this folds
    what is currently quite simple code into the already rather complex
    simplifier.

=== Specialiser issues ===

The specialiser also interacts with rewrite rules in ways I'm not entirely sure I yet understand. See "Note [Specialisations already covered]" in `Specialise.hs`.


== Status ==

There is a preliminary patch set [[https://github.com/bgamari/ghc/tree/wip/rule-context|here]].
```
