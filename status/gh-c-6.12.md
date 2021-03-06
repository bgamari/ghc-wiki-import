CONVERSION ERROR

Original source:

```trac
= Release plans for GHC 6.12 =

As usual, we're planning a major release of GHC around September. 
Here's our list of the main items currently scheduled for 6.12.1, and 
their status.  If you have the time and inclination to help with any of 
these, please get involved!

 * '''Parallel performance'''.  6.12.1 will ship with the improvements to 
 parallel performance described in our ICFP 2009 paper.  Still to do: 
 overhaul the +RTS GC settings, tune for good performance by default.

 * '''Parallel profiling''': the new RTS tracing features will be included, and 
 we hope to have a release of `ThreadScope` to coincide with GHC 6.12.1. 
 `ThreadScope` is written using gtk2hs, and could benefit from someone with 
 expertise in producing polished gtk2hs apps - if you can lend a hand, 
 contact Satnam Singh <satnams@microsoft.com>.

 * '''Unicode I/O''': the new Unicode I/O library is in, and will ship with 
 6.12.1.  Still to do: decide on the public API for changing encodings 
 and newline conversion.

 * '''Shared libraries''': we intend to ship with shared library support on at 
 least x86/Linux and x86-64/Linux.  This is almost done, the remaining tasks are:
   * Turn on `--enable-shared` on Linux x86/x86-64 platforms by default, to shake out as-yet detected bugs. We want this to be the default for ghc-6.12.
   * Change where the RTS libs are stored and use the `-rpath` to select one at exe link time. Then link all shared libs to the rts (without specifying an rpath).
   * Testing testing testing. Try building a subset of hackage using cabal and shared libs. See what fails.
   * Check the user guide is accurate (we're not currently aware of any infidelities).

 * '''Data Parallel Haskell'''.  The goal for DPH for 6.12.1 is to be usable for small applications.  The system will still be highly experimental and use a minimal Prelude substitute, but small dataparallel kernels should see reasonable parallel performance and scalability.  Still to do: we still haven't got a handle on the mysterious code blow up produced by the Simplifier on vectorised code.  Whether or not we can track down and fix that problem in time for 6.12.1 will have a significant impact on the complexity of the dataparallel code that we will support.

 * '''Plugin support in GHC'''.  The patches are not yet in GHC; they are awaiting review by Simon PJ.

 * '''The new backend code generator'''.  At the moment, it seems unlikely that 
 GHC 6.12.1 will ship with the new code generator enabled by default, 
 although it may well be available for testing.  Meanwhile, work on it 
 continues.

The smaller items are all embodied in tickets, here are the tickets 
currently in the 6.12.1 milestone (135):
 * http://hackage.haskell.org/trac/ghc/query?status=new&status=assigned&status=reopened&milestone=6.12.1
and on the 6.12 branch (251):
 * http://hackage.haskell.org/trac/ghc/query?status=new&status=assigned&status=reopened&milestone=6.12+branch

I estimate there are 2 man years of work here - needless to say, we 
aren't going to fix all these tickets :)  As usual, if you want to vote 
for something, add your email to the CC field of the ticket.

Several of these tickets would make good tasks for a fledgling GHC 
hacker.  e.g.  http://hackage.haskell.org/trac/ghc/ticket/2362 (allow 
full import syntax in GHCi) has a lot of support, and is a nice 
self-contained task (but not a small one).

Even if you're not a GHC hacker you can still help, e.g. by helping to 
narrow down the cause of a bug, or verifying a bug on your platform.

Let me remind people that GHC HEAD has the new build system, and it's 
actually rather pleasant to work with.  Even if you have no idea what 
you're doing, you can always say 'make' at the top level and the build 
system will figure out what needs doing (ok, so that's what build 
systems are supposed to do, but GHC's has never quite managed it until 
now!).

```
