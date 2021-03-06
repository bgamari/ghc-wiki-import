## Trying to make more thing known-to-converge



See [NestedCPR](nested-cpr#converges-detection) for context. This attempt turned out to be too involved.



Nested CPR is only sound if we know that the nested values are known to converge for sure. The semantics is clear: If `f` has CPR `<...>m(tm(),)`, then in the body of `case f x of (a,b)`, when entering `a`, we are guaranteed termination.



What is the semantics of an outer `t`? Given `f` with CPR `<L>tm()` and `g` with CPR `<S>tm()`? Does the latter even make sense? If so, should `f undefined` have CPR `m()` or `tm()`? This approach:



The convergence information a function is what holds if its strictness annotations are fulfilled: So if `g x`  has `tm()` if `x` has `t` (possibly because it has previously been evaluated by the caller), otherwise `m()`. `f x` always has `m ()` (presumably because `x` is \_never\_ entered when evaluating `f`).



For the implementation this implies that we need to be careful when `lub`’ing: If one branch is lazy, but not absent in an argument or free variable), and the other branch is strict, then even if both branches claim to terminate, we need to remove the termination flag (as one had the termination under a stronger hypothesis as the hole result) (Feels inelegant.)



Similar thought needs to be considered whenever one goes up in the lattice (better always go up by using `lub` then...)



In pictures: This is the lattice, which is not a simple product lattice any more:


```wiki
       ------ <L><L>------
     /          | \       \
    /           | <L><L>t  \
<S><L>          |           <S><L>  
 |  \ \         |           |  |  \
 |   \ <S><L>t  |          /   |   <S><L>t 
 |    \         |         /    |
 \     \        |        /     |  
  \     ----- <S><S>-----      | 
   \           |    \          |
    \          |     <S><S>t   /
     \         |              /
      ---------⊥-------------/
```


When evaluating a demand type as a demand transformer, we need to compare the convergence flags of the argument expressions with the strictness demands, and possibly adjust the termination information. I believe can be done using `deferAndUse` as before.



What about nested strictness annotations? For now the hypothesis of the demand transformer only requires the arguments (and free variables) to be terminating; if there is a `<S(S)>`, then it should not be marked as converging. Maybe this can be improved later, using nested CPR.



Some implementation implications:


- There is no unit for `lubDmdType` any more. So for case, use `botDmdType` for no alternatives, and `foldr1` if there are multiple.
- Unlifted variables (e.g. `Int#`) are tricky. Everything is strict in them, so for an \*unlifted\* argument, `<L>t` implies `<S>t` and hence `<S>t ⊔ <L>t = <S>t`, and we really want to make use of that stronger equation. But when lub’ing, we don’t know any more if this is the demand for an unlifted type. So instead, the demand type of `x :: Int#` itself is `{x ↦ <L>} t`, while `x :: Int` continues to have type `{x ↦ <S>} t`.
- It is important that functions (including primitive operations and constructors like `I#`) have a strict demand on their unlifted argument. But it turned out to be easier to enforce this in the demand analyser: So even if `f` claims to have a lazy demand on a argument of unlifted type, we make this demand strict before feeding it into the argument.
- **It is no longer safe to remove variables from the demand environment**, if the demand on them is strict. There must be so many bugs lurking around...


Unfortunate example:


```wiki
g :: Int -> Int
g 1 = 0
g x = x
```


What should its signature be? We want `<S>t`, but we get `<S>`. Why? Because the branches have signatures `t` and `{x ↦ S} t`. So their lub is going to be `{x ↦ L}`. In a later step, the demand on `x` will be strict again, but that is not easily visible here.



Can we avoid this? Not if we want to keep using the strictness demand as the hypothesis for the termination information. The alternative would be to to track the demand required separately from strictness. Then the lub of `t` and `{x ↦ required} t` would be `{x ↦ required} t`, and this case would work fine. That would turn it into a completely separate analysis, it seems.


