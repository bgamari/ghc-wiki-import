CONVERSION ERROR

Original source:

```trac
== Packing and Unpacking Heap Graph structures ==

An essential component of Eden as well as other Parallel Haskells for distributed memory is a packing module: traversal/serialisation of a subgraph in the heap, and respective de-serialisation.

In one sentence, packing walks breadth-first through the reachable subgraph from the node to pack, and only copies info-pointer and non-pointers, since pointers can be established from the order inside a packet when unpacking.

The details are a bit more involved, and not much documentation available... 
You should have a look at the wiki page about [wiki:Commentary/Rts/Storage/HeapObjects Heap closure layout] before reading the packing code.


=== TODO: this subject deserves largely more space, and links to GHC commentary pages. ===

==== Pointer tagging ====
Recently, Hans-Wolfgang Loidl spotted a number of issues with [wiki:Commentary/Rts/HaskellExecution/PointerTagging Pointer Tagging] in version 6.8.
The e-mail exchange related to this is documented [wiki:GpHEden/PackingAndPointerTagging here: GpHEden-PackingAndPointerTagging].


==== Spark tagging ====
In GHC-GUM and GHC-SMP a variant of pointer tagging might be useful: spark tagging.
A tag is attached to a spark in the spark pool. This can hold information for example
on the estimated size of a computation. Initially the tag will 0 since we only add pointers
to unevaluated closures. During GC the spark pool is pruned anyway, i.e. pointers to closures
that have become evaluated will be eliminated. Inbetween GCs we only need to follow the spark
pointer when activating a spark or packing the graph. In both cases the RTS routines can 
take care of the untagging.
The plan is to implement such spark tagging in GUM-6.08.


==== Trivia ====

In the GHC-6.13 port, `Pack.c` has been created from the previous version (Eden-6.8.3), doing a one-pass review of the old packing code. Packing and unpacking methods for pointer arrays have been added (should not be used on mutable arrays, though). 
Things to do:
 * reformatting in line with GHC customs
 * code restructuring,
 * directly use an "unpack state", recover from interrupted packing 
 * send partial structure in separate message
The module can be tested by a primitive {{{duplicate#:: a -> IO a}}}, which  packs and unpacks (=> creates a copy of) something in the local heap.

Interesting question is: should the routine be able to pack/unpack _every_ heap object? 
(up to now unable to pack: MVars,  TSOs, all kinds of Stack elements, all STM-related closures)


[wiki:GpHEden --> back to GpHEden]

```
