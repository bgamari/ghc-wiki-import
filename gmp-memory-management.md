# Interaction between GMP and the Storage Manager



The Glasgow Haskell Compiler uses GMP to implement Integer.
GMP expects a storage model where pointers (newtyped as mpz\_t) are explicitly allocated and freed.
However, Haskell requires Integers to be garbage collected.
This could be done with ForeignPtrs, however (presumably for performance reasons?) GHC uses ByteArray\#s from the heap instead.
rts/sm/Storage.c `initStorage` sets three override functions to do this: `stgAllocForGMP`, `stgReallocForGMP`, and `stgDeallocForGMP`.



This requires a fairly subtle interaction in order to work safely,
because heap ByteArray\#s are normal objects and can be moved by garbage collection.
The important thing to note is that the GHC garbage collector is of the stop-the-world variety.
This means that all threads must reach a synchronization point before garbage collection can actually begin.
However, there are no such synchronization points inside the GMP-based primops.
Therefore, garbage collection, and consequent moving of Integers, can only occur when no GMP operations are executing.


