# Local Warning Pragmas


### What is it and why is it needed?



I want to implement possibility to suppress particular kinds of warnings for parts of a source file.



According to [\#602](https://gitlab.staging.haskell.org/ghc/ghc/issues/602):
"One way to achieve this is to allow parts of a file to be delimited by pragmas specifying the warnings to be suppressed, and then filter out the warnings during compilation based on the source location attached to the warning."



Very natural thing is to suppress warnings, that can be thrown by some syntactic elements. To sum up, it would be a nice feature to have warning suppression for four things:


- Functions
- Instances
- Imports
- Type classes


Having source file delimeted by pragmas is not good idea as for me, because it will ruin code clarity and for me, for example, it would be too hard to read such source file. So i think that having a single pragma, attached to a function, instance import or typeclass will be a tool of power and precise. There is a nice example in [
Java](http://docs.oracle.com/javase/7/docs/api/java/lang/SuppressWarnings.html) of suppressing warnings for particular methods in classes


## Use cases



It is not very easy to form a good use case for this but nevertheless. 


1. For example, if you depend on particular library function(if you want to support backward compatability) but in newer version of library this function marked as deprecated and so you get warnings about it. Here it may be useful to suppress them instead of rewriting the whole codebase. I have asked about particular use cases on [
  Haskell's reddit cahnnel](https://www.reddit.com/r/haskell/comments/3rbpb6/examples_of_warnings_in_haskell/) and some people need to support old libraries. For example `CRandT` in `monadcryptorandom-0.6.1`:


Many instances, e.g. `MonadTrans` have `Error e` constraint, which is deprecated in the newer `transformers`.
In `monadcryptorandom-0.7.0` `CRandT` is implemented using `ExceptT` so constraint is gone.



To be more precise, here is the code example


```
instance Monoid DivByError where
  mempty = Other "guard error"
```

```
Prelude> :r
[1 of 1] Compiling Fp11WithExcept   ( fp11WithExcept.hs, interpreted )

fp11WithExcept.hs:123:10: Warning:
    No explicit implementation for
      `mappend'
    In the instance declaration for `Monoid DivByError'
Ok, modules loaded: Fp11WithExcept.
```


So here we are getting a warning that we want so suppress. This example is given by my lecturer. Here we replacing `ErrorT` transformer for `ExceptT`.
  


1. Recent monad of no return proposal suggests that having `Applicative` context sufficed for `Monad` assumes that `return` is already implemented as `pure`, so we don't need to duplicate code. However, Monad still has minimal complete definition `>>=` and `return`, so we can have warnings about incomplete minimal definition.

## Exempli gratia



I don't know conventions about naming pragmas, so let it be something like this.


```
module Test where

import old_lib (foo) 

...

bar :: a -> b -> c 
{-# SUPPRESS bar #-}
bar x y = foo y $ x
```


We are suppressing warnings for one particular function. By writing `{-# SUPPRESS bar #-}` i mean that all warnings that function `bar` throws will be suppressed. 



Another example:


```

module Test where

{-# SUPPRESS foo #-}
foo :: Integer -> Integer
foo n | n >= 0 = fac n
      | n < 0 = n + 1
        where
            fac x | x == 0 = 1
                  | x /= 0 = x * fac (x - 1)
foo _ = undefined

```


With -Wall GHC will warn us about incomplete pattern matching for `fac` however, `fac` is used and defined only when `n >= 0`. So we write `{-# SUPPRESS foo #-}` and this warning(and others that `foo` throws) will be suppressed and not printed to user.


