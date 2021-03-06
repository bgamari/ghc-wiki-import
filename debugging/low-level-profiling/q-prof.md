CONVERSION ERROR

Original source:

```trac


== Using qprof ==

[http://www.hpl.hp.com/research/linux/qprof/ qprof] is a tool for quick profiling of executables.  It can give you an idea of where the program is spending most of its time without recompiling the program.  It works by hooking a few functions using LD_PRELOAD, and installing a SIGVTALRM signal handler to sample the program counter.

== Notes for using it with GHC ==

Installing qprof on most Linux distributions should be easy; e.g. on Debian-based systems use `sudo apt-get install qprof`.

Profiling a program is straightforward:

{{{
qprof ./prog <flags>
}}}

The profile will be by basic-block, so you'll need some combination of `-ddump-simpl -ddump-stg -ddump-cmm` to map back to the Haskell code.

I got some strange results when using the non-threaded RTS, such as the `handle_tick` function being high up the profile. Presumably because we also use SIGVTALRM.  Using the threaded RTS or `+RTS -V0` should help here.

I found the resolution of the samples to be way too low.  You can increase the frequency of samples with qprof's `-i` option, but I wasn't able to increase it beyond about 1ms (the default is 10ms).  Nevertheless, this is a good way to identify the inner loop quickly.  It's not a good way to evaluate small optimisations (for that, PAPI or oprofile would be better).

```
