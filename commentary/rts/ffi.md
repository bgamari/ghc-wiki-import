CONVERSION ERROR

Original source:

```trac


= GHC Commentary: Runtime aspects of the FFI =


== Foreign Import "wrapper" ==
Files [[GhcFile(rts/Adjustor.c)]]  [[GhcFile(rts/AdjustorAsm.S)]].

Occasionally, it is convenient to treat Haskell closures as C function pointers.
This is useful, for example, if we want to install Haskell callbacks in an existing C library.
This functionality is implemented with the aid of adjustor thunks.

An adjustor thunk is a dynamically allocated code snippet that allows
Haskell closures to be viewed as C function pointers.

Stable pointers provide a way for the outside world to get access to,
and evaluate, Haskell heap objects, with the RTS providing a small
range of ops for doing so. So, assuming we've got a stable pointer in
our hand in C, we can jump into the Haskell world and evaluate a callback
procedure, say. This works OK in some cases where callbacks are used, but
does require the external code to know about stable pointers and how to deal
with them. We'd like to hide the Haskell-nature of a callback and have it
be invoked just like any other C function pointer.

Enter adjustor thunks. An adjustor thunk is a little piece of code
that's generated on-the-fly (one per Haskell closure being exported)
that, when entered using some 'universal' calling convention (e.g., the
C calling convention on platform X), pushes an implicit stable pointer
(to the Haskell callback) before calling another (static) C function stub
which takes care of entering the Haskell code via its stable pointer.

An adjustor thunk is allocated on the C heap, and is called from within
Haskell just before handing out the function pointer to the Haskell (IO)
action. User code should never have to invoke it explicitly.

An adjustor thunk differs from a C function pointer in one respect: when
the code is through with it, it has to be freed in order to release Haskell
and C resources. Failure to do so will result in memory leaks on both the C and
Haskell side.


----
CategoryStub


```
