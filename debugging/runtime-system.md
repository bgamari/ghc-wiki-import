CONVERSION ERROR

Original source:

```trac


== Debugging the runtime system ==

If you link with `-debug` you'll get the "debugging RTS" (the debugging [wiki:/Commentary/Rts/CompilerWays way]).  This has some extra consistency checking (which makes it run a little slower), and has lots of logging infrastructure to tell what is going on.

The extra features are controlled with the `-D` runtime system flag. So you might say
{{{
sh$ ghc -o MyProg Main.hs
sh$ ./MyProg +RTS -Ds
}}}
to see logging information from the scheduler.

Use `+RTS -?` to see a list of all the `-D` flags. The current list (GHC 7.8) is:
{{{
  -Ds  DEBUG: scheduler
  -Di  DEBUG: interpreter
  -Dw  DEBUG: weak
  -DG  DEBUG: gccafs
  -Dg  DEBUG: gc
  -Db  DEBUG: block
  -DS  DEBUG: sanity
  -Dt  DEBUG: stable
  -Dp  DEBUG: prof
  -Da  DEBUG: apply
  -Dl  DEBUG: linker
  -Dm  DEBUG: stm
  -Dz  DEBUG: stack squeezing
  -Dc  DEBUG: program coverage
  -Dr  DEBUG: sparks
}}}
Of these, `-DS` (sanity checking) is special. It switches on lots of expensive consistency checks, including a full heap consistency check which runs after every garbage collection.  Your program will run a lot slower, but it helps when tracking down garbage-collection errors.

== Debugging memory leaks ==

There are three kinds of memory leaks:

=== The GC is retaining something when you think it shouldn't ===

This doesn't come up very often, to be honest.  If it happens then you need to trace back through the object graph to find the path that lead to the offending object being retained by the GC.  This is quite tedious to do using gdb, but I don't know of a better way.  See [wiki:Debugging/CompiledCode].

=== Some malloc'd memory has not been free'd ===

Fortunately this one is quite easy.  Compile the program with `-debug`, and run it under [http://valgrind.org/ Valgrind], with the options `--leak-check=full --show-reachable=yes`, to get a full report on leaking memory and where it was allocated from.  Some leaks are expected: for example, we don't release the heap data structures during shutdown of a standalone program, because there might be outstanding foreign calls referring to data in the heap.

=== Some blocks allocated from the block allocator have not been freed ===

The debugging RTS checks for these leaks itself, and complains very loudly if it finds any.  Just compile the program with `-debug` and run it.  The code to check for leaks is in [[GhcFile(rts/sm/Sanity.c)]], `memInventory`.






```
