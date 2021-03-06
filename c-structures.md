# Support C structures in Haskell FFI



This page describes language extension proposal to extend Haskell FFI with support for C structures.
See also the [\#9700](https://gitlab.staging.haskell.org/ghc/ghc/issues/9700).


## The issue we are trying to solve



According to Haskell Report [
8.4.2](http://www.haskell.org/onlinereport/haskell2010/haskellch8.html#x15-1560008.4.2)


>
>
> Only a subset of Haskell’s types are permissible as foreign types, as only a restricted set of types can be canonically transferred between the Haskell context and an external context.
>
>


Briefly, this subset is limited to basic foreign types, their synonyms and newtypes. Obviously there is no way to represent C structure as foreign type in Haskell. 



Usually C libraries use C structures in their API for two reasons:


- *Convenience*
  One may need to return more then one value or aggregate multiple arguments, but doesn't want to bother with heap allocation.
- *Performance*
  On some architectures registers can be used to pass (return) small structures to (from) functions. That can result in significant performance boost compared to memory allocated structures.


To work with such libraries, we have to write special C functions to wrap original API into Haskell-friendly one. That is inconvenient, error prone and may add performance penalty.


## Proposal



I propose to extend the definition of marshallable foreign types with the following clause:


>
>
> a type **T t'<sub>1</sub>** ... **t'<sub>n</sub>** where **T** is defined by data declaration
>
>
> >
> >
> > *data* **T a<sub>1</sub>** ... **a<sub>n</sub>** = **C t<sub>1</sub>** ... **t<sub>k</sub>**
> >
> >
>
>
> and
>
>

- **k** \> 0
- the constructor **C** is visible where **T** is used
- **t<sub>1</sub>**\[**t'<sub>1</sub>**/**a<sub>1</sub>** ... **t'<sub>n</sub>**/**a<sub>n</sub>**\] ... **t<sub>k</sub>**\[**t'<sub>1</sub>**/**a<sub>1</sub>** ... **t'<sub>n</sub>**/**a<sub>n</sub>**\] are marshallable foreign types


This data type should be marshaled to/from C structure with **k** fields of corresponding C types with natural alignment. Packed structures are not allowed.



Recursive data types are not allowed.
Nested structures are allowed.
Structures with one field are allowed.



The extension should be enabled via `CStructures` language pragma.


### Examples



Here is few examples of C declarations and corresponding Haskell foreign import declaration. 


- Returning C structure

```wiki
struct S {
  int i;
  char c;
};

struct S test(int i);
```

```wiki
data S = S Int Char

foreign import ccall "c_test" test :: Int -> S
```

- Passing C structure as argument

```wiki
struct S {
  int i;
  char c;
};

int test(int, struct S);
```

```wiki
data S = S Int Char

foreign import ccall "c_test" test :: Int -> S -> Int
```

## Implementation



Implementation can flatten structures and produce ccall with multiple arguments and return values of basic foreign types. The layout of arguments and return value should be carried around and passed way down to code generator to be used to generate platform specific code according to calling convention.


### Desugaring examples



Here are examples how foreign import declaration can be desugared. It is simplified slightly --
e.g. RealWorld token handling, coercions and C type layout annotations are omitted.


- Returning C structure

```wiki
data S = S Int Char

foreign import ccall "c_test" test :: Int -> S
```

```wiki
test = \arg ->
  case arg of
    I# arg_ ->
      case ccall c_test arg_ of
        (int_res_, char_res_) -> S (I# int_res_) (I# char_res_)
```

- Passing C structure as argument

```wiki
data S = S Int Char

foreign import ccall "c_test" test :: Int -> S -> Int 
```

```wiki
test -> \arg1 arg2 ->
  case arg1 of
    I# arg1_ ->
      case arg2 of
        S (I# int_arg_) (I# char_arg_) ->
          case ccall c_test arg1_ int_arg_ char_arg_ of
            res_ -> I# res_
```

### Code generator



I implemented support for C structures as return type in cmm [
here](https://phabricator.haskell.org/D252). It covers most supported architectures. The amount of platform dependent code is reasonable and it is localized in `genCCall32'` and `genCCall64'` and their helpers.


