# Overview



This page documents a proposed syntactical extension called `ArgumentDo`. The feature request is tracked at [\#10843](https://gitlab.staging.haskell.org/ghc/ghc/issues/10843).



This extension would allow a `do` block, a lambda, and a few other syntactic constructs to be placed directly as a function argument, without parentheses or a `$`. For example,


```
atomically do
  v <- readTVar tv
  writeTVar tv $! v + 1
```


would be equivalent to


```
atomically (do
  v <- readTVar tv
  writeTVar tv $! v + 1)
```


and


```
withForeignPtr fptr \ptr -> c_memcpy buf ptr size
```


would be equivalent to


```
withForeignPtr fptr (\ptr -> c_memcpy buf ptr size)
```

# Changes to the grammar



The Haskell report [
defines](https://www.haskell.org/onlinereport/haskell2010/haskellch3.html#x8-220003) the `lexp` nonterminal thus:


```wiki
lexp 	→ 	\ apat1 … apatn -> exp 	    (lambda abstraction, n ≥ 1)
	| 	let decls in exp 	    (let expression)
	| 	if exp [;] then exp [;] else exp 	    (conditional)
	| 	case exp of { alts } 	    (case expression)
	| 	do { stmts } 	    (do expression)
	| 	fexp 

fexp 	→ 	[fexp] aexp 	    (function application)
 
aexp 	→ 	qvar 	    (variable)
	| 	gcon 	    (general constructor)
	| 	literal
	| 	( exp ) 	    (parenthesized expression) 
        ...
```


which means lambda, `let`, `if`, `case`, and `do` constructs cannot be used as either LHS or RHS of a function application. GHC Haskell has a few more constructs that fall into this category, such as `mdo`, `\case` and `proc` blocks.



The `ArgumentDo` extension would allow all these constructs in argument positions. However, there is one technical problem: some of the constructs (e.g. lambdas) do not have a marker at the end, so it would create ambiguities if we simply moved their production rules to `aexp`. For example, `f \a -> a b` would become ambiguous between `f (\a -> a) b` and `f (\a -> a b)`. We'd like to always favor the latter interpretation.



For this reason, we split these constructs into 2 groups:


- **Group A** constructs are those whose end is marked with a designated token. For example, `do` blocks fall into this group because they always end with a `}` (which may be generated by the layout rules). This group includes `do`, `case`, `\case` and `mdo` constructs.
- Everything else is in **group B**. It includes lambdas, `if`, `let`, and `proc` constructs.


**Group A** constructs would be moved to the `aexp` production rule, allowing them to be used as the LHS as well as the RHS of an function application. **Group B** constructs are only allowed as the RHS, and moreover, they can only appear unparenthesized when they are the last argument in the application chain.



This yields the following proposed rules:


```wiki
lexp 	→ 	[fexp] openexp      (function application)
        |       fexp

-- Group B constructs here
openexp →      \ apat1 … apatn -> exp 	    (lambda abstraction, n ≥ 1)
	| 	let decls in exp 	    (let expression)
	| 	if exp [;] then exp [;] else exp 	    (conditional)

fexp    →      [fexp] aexp 	    (function application)

aexp 	→ 	qvar 	    (variable)
	| 	gcon 	    (general constructor)
	| 	literal
	| 	( exp ) 	    (parenthesized expression) 
        -- Group A constructs here
	| 	case exp of { alts } 	    (case expression)
	| 	do { stmts } 	    (do expression)

        ...
```

# Less obvious examples


## Deleting parentheses



This extension will most often allow deletion of just one `$` operator per application. However, sometimes it does more. For example, in the following example, you can't simply replace the parentheses with a `$`:


```
pi + f (do
  ...
  )
```


With `ArgumentDo`, you would be able to write the following instead:


```
pi + f do
   ...
```

## Multiple block arguments



A function may take multiple `do` blocks:


```
f do{ x } do { y }
```


or


```
f
  do x
  do y
```

## Block as a LHS



A `do` block can be LHS of a function application:


```
do f &&& g
x
```


would just mean


```
(f &&& g） x
```

# Design space



Possible modifications to the proposal include:


- Only allow `do` in argument positions, but no other constructs. This has an advantage of making a minimal change to the grammar, while addressing the most common case.
- Only allow **group A** constructs in argument positions. This has an advantage of not making the grammar more complex.
- Add `{-# SCC #-}` and `{-# CORE #-}` constructs to **group B**. This is arguably a more uniform treatment, but it has a problem that an insertion of a pragma can change the parse tree. For example, `f a {-# SCC "foo" #-} b c` would parse as `f a ({-# SCC "foo #-} (b c))`

# Discussion



This proposal has been extensively discussed on [
haskell-cafe](https://mail.haskell.org/pipermail/haskell-cafe/2015-September/121217.html) and on [
reddit](https://www.reddit.com/r/haskell/comments/447bnw/does_argument_do_have_a_future/).



On the mailing list I see roughly 13 people in favor of the proposal and 12 people against it. Some major opinions (mostly copied from [
bgmari's summary](https://ghc.haskell.org/trac/ghc/ticket/10843#comment:12)).


## Pros


- It's easier to read than the alternative.
- This extension removes syntactic noise.
- This makes basic do-syntax more approachable to newbies; it is a commonly asked question as to why the $ is necessary.
- This simplifies the resulting AST, potentially making it simpler for editors and other tools to do refactoring.
- It's something that belongs in the main language, and if its something we'd consider for a mythical Haskell', it has to start as an extension.
- It gets rid of some cases where using $ doesn't work because $ interacts with other infix operators being used in the same expression.
- This would make do blocks consistent with record creation, where parentheses are skipped, allowing things such as return R { x = y}
- This does not change the meaning of any old programs, only allows new ones that were previously forbidden.
- This gets rid of the need for a specially-typed $ allowing runSt $ do ... 

## Cons


- It's harder to read than the alternative.
- Creating a language extension to get rid of a single character is overkill and unnecessary.
- You can already get rid of the $ by just adding parentheses.
- More and more syntactic "improvements" just fragment the language.
- Although this is consistent with record syntax, record syntax without parents was a mistake originally.
- It does not make the language more regular.


[
Richard Eisenberg has suggested](https://mail.haskell.org/pipermail/haskell-cafe/2015-September/121311.html) that this proposal makes the language more regular by reducing the number of nonterminals by one. Unfortunately I (Akio) don't think this is the case. If we allow **group B** constructs (defined above) in argument positions, we have to actually increase the number of nonterminals. If we don't, we can't get rid of the `lexp` nonterminal, which leaves the number of nonterminals unchanged.

