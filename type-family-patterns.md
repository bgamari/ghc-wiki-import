CONVERSION ERROR

Original source:

```trac
This page describe implementation plan for ticket  #8109 (Type family patterns should support as-patterns.)

The idea is to introduce support for as-pattens in type families that may make
it easier to write complex rules. Examples:

== Code examples ==

{{{
#!haskell
type family Last u where
   Last [x] = x
   Last (x ': xs@(_ : _)) = Last xs
}}}

Similar example (taken from #8109):

{{{
#!haskell
instance SingI n => C '[n] where
    type T '[n] = Integer
    val _ = fromNat (P :: P n)

instance (SingI n, C ns) => C (n ': ns@(_ ': _)) where
    type T (n ': ns) = (Integer, T ns)
    val _ = (fromNat (P :: P n), val (P :: P ns))
}}}

Another example taked from #9608:

{{{
#!haskell
type family XOut m1 m2 where
    XOut a@(ValueS vbase vs) (MonadCtx2Dummy' m2 s2 env set m s) = MonadCtx2Dummy' m2 s2 (XEnv env a) (XSet set a) m (XSafety s a)
}}}

== Implementation plan ==

1. Add nodes into AST Tree to support as-patterns.

2. Add support in typechecker

3. Test that changes doesn't break existing test suite. (Push to Phabricator for first review)

4. Add type familes as-patterns support to parser

5. Add tests.


== Implementation progress ==

Currently implementor is reading documentation and adding information to the implementation plan.

== Pending questions == 

 
```