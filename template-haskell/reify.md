# A better Reify



[ticket:11832](https://gitlab.staging.haskell.org/ghc/ghc/issues/11832)



There are a few projects using quasiquotes to interface with other languages. At least C (`language-c-inline`, `inline-c`), R (`inline-r`), and there are ongoing efforts for Java, Javascript and other languages.



A common pattern in these libraries is to collect the content of all quasiquotes that appear in a module, generate a foreign source file based on them, and then link the object code resulting from compiling it.



When the foreign language is itself statically typed, we need to make
sure we generate code with proper type annotations, including for any
antiquotation variables. In older versions of GHC, we could derive the
type annotations from the inferred Haskell types, but this is not
possible since 7.8 because the types of any variable in the current
"declaration group" are not made available (wiki:[TemplateHaskell/BlogPostChanges](template-haskell/blog-post-changes)). So libraries like inline-c
ask for some extra verbosity from the user:


```wiki
mycossin1 :: Double -> IO Double
mycossin1 somex = [cexp| double { cos($(double somex)) * sin($(double
somex)) } |]
```


The type annotations inside the quasiquote are redundant with the
explicitly provided type signature. C types are typically short
enough, but other languages (e.g. C++, Java), have really long fully
qualified type names, so the extra annotations are a cost.



The are good reasons why types aren't available from within a
declaration group. But by the time type checking of the whole module
is finished, types are fixed once and for all. So the question is:



Could we make it possible to query the types of local variables at
the end of type-checking?



Template Haskell already provides `addModFinalizer`. You feed it a `Q`
action, which will run once the whole module is type checked. If at
that point we could ask, "what's the type of `somex`?", then we
could let the user write


```wiki
mycossin2 :: Double -> IO Double
mycossin2 somex = [cexp| cos($somex) * sin($somex) |]
```


and yet still generate a C file capturing the content of above
quasiquote with the right types:


```wiki
double module_foo_qq1(double v1)
{
    return (cos(v1) * sin(v1));
}
```


since we know that `somex :: Double` and that the Haskell type `Double`
corresponds to C's `double` primitive type.



Bound variables have a unique name associated to them, which we can
get hold of in Template Haskell using the 'var syntax, but


```wiki
f x = $(let name = 'x in addModFinalizer (reify name >>= \info ->
runIO (print info)) >> [| x |])
```


results in a compiler error,


```wiki
    The exact Name `x' is not in scope
      Probable cause: you used a unique Template Haskell name (NameU),
      perhaps via newName, but did not bind it
      If that's it, then -ddump-splices might be useful
```


because by the time the TH finalizer runs, we're no longer in the scope of `x`.



Can we do anything to improve these use cases of `reify`?


## Some approaches


1. Have `addModFinalizer` capture the local typing environment at the call site, and run the finalizer on it when type checking completes.
1. Have a new function `addGroupFinalizer` like `addModFinalizer`, which should also capture the local typing environment, and that will run the finalizer on it when typechecking of the current declaration group completes. 
1. Allow `reify` to observe out of scope local variables provided that their names are unique within the module.


(1) and (2) are easy to implement for typed splices, which run in the typechecker. Unfortunately, untyped splices run in the renamer, the local typing environment is not yet populated, and thus it cannot be captured. We could either compromise to implement `addGroupFinalizer` in typed splices only, or we would need to overcome this obstacle with a more involved implementation.



Someone more informed could comment on (3).


### Implementing `addGroupFinalizer` in untyped splices



A proposal:


- When the renamer runs `addGroupFinalizer` it annotates the AST with a mutable reference at the location of the splice.

  ```wiki
  r <- newIORef (Nothing :: Maybe LocalTypeEnv
  ```

  Perhaps the annotation could be implemented by extending the `Tickish` datatype with a new data constructor.
- When the type checker finds the annotation it writes to it the local typing environment:

  ```wiki
  getLclEnv >>= writeIORef r . Just . tcl_env
  ```
- When the finalizer runs, it reads its local typing environment from the mutable reference.


The local renamer environment should be copied as well, but that wouldn't change the discussion, I think.



Also, instead of mutable references we could generate integer keys and introduce a map of keys to local typing environments.

