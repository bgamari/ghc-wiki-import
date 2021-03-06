


# GHC Commentary: [Libraries/Integer](commentary/libraries/integer)



GHC is set up to allow different implementations of the `Integer` type to be chosen at build time.


## Selecting an Integer implementation



You can select which implementation of Integer is used by defining `INTEGER_LIBRARY` in `mk/build.mk`. This tells the build system to build the library in `libraries/$(INTEGER_LIBRARY)`, and the `cIntegerLibrary` and `cIntegerLibraryType` values in `Config.hs` are defined accordingly.



The default value is `integer-gmp`, which uses the [
GNU Multiple Precision Arithmetic Library (GMP)](http://gmplib.org/) to define the Integer type and its operations.



The other implementation currently available is `integer-simple`, which uses a simple (but slow, for larger Integers) pure Haskell implementation.


## The Integer interface



All Integer implementations should export the same set of types and functions from `GHC.Integer` (within whatever `integer` package you are using). These exports are used by the `base` package. However, all of these types and functions must actually be defined in `GHC.Integer.Type`, so that GHC knows where to find them.
Specifically, the interface is this:


```
data Integer 

mkInteger :: Bool   -- True <=> non-negative
          -> [Int]  -- Absolute value in 31 bit chunks, least significant first
                    -- ideally these would be Words rather than Ints, but
                    -- we don't have Word available at the moment. (why?)
          -> Integer
    
smallInteger  :: Int# -> Integer
integerToInt  :: Integer -> Int#
 
wordToInteger :: Word# -> Integer
integerToWord :: Integer -> Word#

-- And similarly for Int64#, Word64# on 64-bit

floatFromInteger  :: Integer -> Float#
decodeFloatInteger :: Float# -> (# Integer, Int# #)
encodeFloatInteger :: Integer -> Int# -> Float#

-- And similarly Double

plusInteger :: Integer -> Integer -> Integer
-- And similarly: minusInteger, timesInteger, negateInteger,
-- eqInteger, neqInteger, absInteger, signumInteger,
--  leInteger, gtInteger, ltInteger, geInteger, compareInteger,
--  divModInteger, quotRemInteger, quotInteger, remInteger,
--  andInteger, orInteger, xorInteger, complementInteger,
--  shiftLInteger, shiftRInteger,
--  hashInteger,
```

## How Integer is handled inside GHC


- **Front end**.  Integers are represented using the `HsInteger` constructor of `HsLit` for the early phases of compilation (e.g. type checking)

- **Core**.  In `Core` representation, an integer literal is represented by the `LitInteger` constructor of the `Literal` type. 

  ```
  data Literal = ... | LitInteger Integer Type
  ```

  While `Integer`s aren't "machine literals" like the other `Core` `Literal` constructors, it is more convenient when writing constant folding RULES to pretend that they are literals rather than having to understand their concrete representation. (Especially as the concrete representation varies from package to package.) We also carry around a `Type`, representing the `Integer` type, in the constructor, as we need access to it in a few functions (e.g. `literalType`).

- **Constant folding**.  There are many constant-folding optimisations for `Integer` expressed as built-in rules in [compiler/prelude/PrelRules.lhs](/trac/ghc/browser/ghc/compiler/prelude/PrelRules.lhs); look at `builtinIntegerRules`.  All of the types and functions in the `Integer` interface have built-in names, e.g. `plusIntegerName`, defined in [compiler/prelude/PrelNames.lhs](/trac/ghc/browser/ghc/compiler/prelude/PrelNames.lhs) and included in `basicKnownKeyNames`. This allows us to match on all of the functions in `builtinIntegerRules` in [compiler/prelude/PrelRules.lhs](/trac/ghc/browser/ghc/compiler/prelude/PrelRules.lhs), so we can constant-fold Integer expressions. An important thing about constant folding of Integer divisions is that they depend on inlining. Here's a fragment of `Integral Integer` instance definition from `libraries/base/GHC/Real.lhs`:

  ```
  instance Integral Integer where
      toInteger n      = n

      {-# INLINE quot #-}
      _ `quot` 0 = divZeroError
      n `quot` d = n `quotInteger` d
  ```

  Constant folding rules for divisions are defined for `quotInteger` and other division functions from `integer-gmp` library. If `quot` was not inlined constant folding rules would not fire. The rules would also not fire if call to `quotInteger` was inlined, but this does not happen because it is marked with NOINLINE pragma - see below.

- **Converting between Int and Integer**.  It's quite commonly the case that, after some inlining, we get something like `integerToInt (intToInteger i)`, which converts an `Int` to an `Integer` and back.  This *must* optimise away (see [\#5767](https://gitlab.staging.haskell.org/ghc/ghc/issues/5767)).  We do this by requiring that the `integer` package exposes

  ```
  smallInteger :: Int# -> Integer
  ```

  Now we can define `intToInteger` (or, more precisely, the `toInteger` method of the `Integral Int` instance in `GHC.Real` ) thus

  ```
  toInteger (I# i) = smallInteger i
  ```

  And we have a RULE for `integerToInt (smallInteger i)`.

- **Representing integers**.  We stick to the `LitInteger` representation (which hides the concrete representation) as late as possible in the compiler.   In particular, it's important that the `LitInteger` representation is used in unfoldings in interface files, so that constant folding can happen on expressions that get inlined.  

>
>
> We finally convert `LitInteger` to a proper core representation of Integer in [compiler/coreSyn/CorePrep.lhs](/trac/ghc/browser/ghc/compiler/coreSyn/CorePrep.lhs), which looks up the Id for `mkInteger` and uses it to build an expression like `mkInteger True [123, 456]` (where the `Bool` represents the sign, and the list of `Int`s are 31 bit chunks of the absolute value from lowest to highest).
>
>

>
>
> However, there is a special case for `Integer`s that are within the range of `Int` when the `integer-gmp` implementation is being used; in that case, we use the `S#` constructor (via `integerGmpSDataCon` in [compiler/prelude/TysWiredIn.lhs](/trac/ghc/browser/ghc/compiler/prelude/TysWiredIn.lhs)) to break the abstraction and directly create the datastructure.
>
>

- **Don't inline integer functions**.  Most of the functions in the Integer implementation in the `integer` package are marked `NOINLINE`. For example in `integer-gmp` we have

  ```
  plusInteger :: Integer -> Integer -> Integer
  plusInteger (S# i1) (S# i2) = ...
  plusInteger (S# i1) (J# j1 j2) = ...
  -- ...two more cases...
  ```

  Not only is this a big function to inline, but inlining it typically does no good because the representation of literals is abstact, so no pattern-matching cancellation happens.  And even if you have `(a+b+c)`, the conditionals mean that no cancellation happens, or you get an exponential code explosion!
