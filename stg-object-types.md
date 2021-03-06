CONVERSION ERROR

Original source:

```trac
Back to GarbageCollectorNotes

[This page needs lots of work]

= Object Structure = 

= Evaluation 101 = 

= Interesting Object Types = 

 * CONSTR - constructors
 * THUNK - thunks (courtesy of being a lazy language)
 * FUN - functions
 * IND - indirections - what is left behind after thunk evalution since we dont know who all would be referring to the thunk. 
 * TSO - thread state object - see discussion at CapabilitiesAndScheduling
 * PAP - partial application - a function object to which only some arguements have been applied.


= Backward Pointers = 

Backwards pointers are the cause of much heartache in writing generational GCs. They are essentially pointers from older generations to newer ones. If you think about it, such a thing should never really occur in a pure functional language, since objects cannot be updated once created. While that is true in essence, backward pointers do arise in Haskell in the following cases

 * Thunk updation
 * Unsafe pointer updation 
   * GHC.Prim
   * usafePerformIO : Haskell.IO.Unsafe

Types
 * MUT_VAR
 * MUT_ARRAY
 * MVAR, TVAR
 * TSO
 * IND_OLDGEN


= Spark Pool and par = 

= Weak References = 

= Stable Pointers = 

= CAF = 
 CAF stands for Constant Applicative Form. 

= Black Holes = 
 * Free closures environment variables early
 * Loop detection at runtime
 * Avoid re-evaluation of the same thunk by mutliple threads

= Mutable List = 
The mutable list, sometimes referred to as the Remember Set is a list of pointers to objects in a generation that point to older generations. When a GC occurs till generation N, generations older than N are not traversed. Instead only the mutable lists of those generations are traversed to see what objects in the new generation are alive, because older generation objects point to them. 


A mutable list per generation is maintained by each capability. When GC starts, these are swept into each generation’s mut_list member and are traversed later. When garbage collecting only till generation N, mut lists of generations <=N are ignored (freed up), since we know that we are going to walk all the objects in those generations anyway.

Certain object like MUT_ARR_PTRS_CLEAN are always on the mut list. In the case of some objects, walking the mut list does not let us take the objects off the mut list. Such objects are added back to the mut list (done by recordMutableGen). 

Objects that can be “cleaned” are cleaned by the function scavenge_one(). By cleaning we mean that the object in question no longer points to a newer generation object. This can be done only by moving the newer generation objects up to the generation, where the original object resides. Code that does this for an object like a THUNK would look like this - 

{{{
end = (StgPtr)((StgClosure *)p)->payload + info->layout.payload.ptrs;
for (q = (StgPtr)((StgClosure *)p)->payload; q < end; q++) {
    *q = (StgWord)(StgPtr)evacuate((StgClosure *)*q);
}
}}}

Objects referred to in the newer generation are moved into the older generation by setting the evac_gen global and calling the evacuate() function.

----
Questions

 * Why does MUT_ARR_PTRS_CLEAN always exist in the mut list?
 * Why is failed_to_evac a global?
 * Why do the steps get scavenged immediately after mut_list is scavenged?
{{{
    for (g = RtsFlags.GcFlags.generations-1; g > N; g--) {
      IF_PAR_DEBUG(verbose, printMutableList(&generations[g]));
      scavenge_mutable_list(&generations[g]);
      evac_gen = g;
      for (st = generations[g].n_steps-1; st >= 0; st--) {
	scavenge(&generations[g].steps[st]);
      }
    }
}}}


```
