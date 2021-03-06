
Some ideas related to [\#7994](https://gitlab.staging.haskell.org/ghc/ghc/issues/7994):



Here are some ideas.



Consider this expression `e`:


```wiki
let f = λ x. let h = f (g1 x)
             in λ y.  x `seq` h (g2 y)
in ...f a 2..... f 3 4 ...
```


This can be rewritten to


```wiki
let f = λ x. let h = f (g1 x)
             in λ y. x `seq`  h (g2 y)
    r = ...f a 2..... f 3 4 ...
in r
```


how can we analyse that to achieve the desired effect (i.e. arity 2 for `f` and hence strict in `x`, and `λ y` one-shot)?



We need to calculate the demand on `f` and `r` in the fix point analysis, instead of always starting with a `cleanEvalDmd` in `dmdAnalRhs`.



So thinking of the mutual recursion as simple recursion with a tuple, an incoming demand `d` on all of `e` is turned into an initial demand `(Abs, d)` on `(f,r)`.



The first iteration analyses `f` with demand `Abs` (i.e. not at all) and `r` with demand `d`, while using `botDmdType` for `f` in the environment of the analyser. Then the result of this analysis is a demand type with `{f → C(C¹(S)), r → Abs, a → Hyper }`.



We lub this with the demand from the outside `(Abs, d)` and re-start with `(C(C¹(S)), d)`. `f` is still as diverging in the strictness signature. But this, we will also get the demand from `f` on `f` into the result. But if we start with `C(C¹(C))` for `f`, then `f` puts that on itself! The resulting demand is thus the same (`{f → C(C¹(S)), r → Abs, a → Hyper }`), but in this pass, we will also have found out something about `f` strictness signature, which we now can consider to be `<S><L>`. Note that we were allowed to create this signature with two arguments, because the demand had two levels of calls.



The demand stayed the same, but the strictness signature changed. So we do another iteration. This now relaxes the demand on `a` and we obtain `{f → C(C¹(S)), r → Abs, a → S }`, with `f` strictness signature unchanged.



A last round and the demand put on `f` by its RHS and `r` stays the same, as well as `f` demand signature, and we have the desired results.



In the counter-example


```wiki
let f = λ x. let h = f (g1 x)
             in λ y... map h...
in ...f 1 2..... f 3 4 ...
```


the second iteration would try `C(C¹(S))` on `f` as before, but it would be `C(S)` in the third run, because of the demand put on `f` by `f` itself.



The problem with this approach is that it is not monotone, as we update the strictness signatures. It might be `<S><L>` in one iteration (which causes `(f x)` to put a lazy demand on `x`) and `<S>` in the next (causing `(f x)` to put a strict demand on `x`). So the fixed-point iteration might not terminate...



Maybe it is enough to over-approximate the demand within the fixed-point iteration? I.e. if `f` has signature `<S><L>`, then `(f x)` puts a strict demand on `x`. Presumably if in the next iteration, `f` has only `<S>`, then nothing went wrong; if it then has `<L>`, we can still reduce the demand on `x`. Note that we always start from a strong demand and converge towards a weaker demand.


