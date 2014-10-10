CONVERSION ERROR

Original source:

```trac
= Shaking up GHC =

This page is a working document to capture the ongoing work on migrating the current GHC build system based on `make` into a new and (hopefully) better one based on `Shake`. See [wiki:Building/Architecture] and [wiki:Building/Using] for more details on the current implementation. For general reference to `Shake`, the first sources to try are:
* http://hackage.haskell.org/package/shake/docs/Development-Shake.html (Haddock docs)
* https://github.com/ndmitchell/shake/blob/master/docs/Manual.md#readme (User manual)

== The goal ==

The existing build system performs the following major steps:

* '''boot''': run `autoconf` on {`configure.ac`, ...} to produce the `configure` script, as well as `libraries/*/GNUmakefile` and `libraries/*/ghc.mk` files. 

* '''configure''': take a bunch of `*.in` files {`config.mk.in`, `ghc.cabal.in`, `ghc-bin.cabal.in`, ...} and generate {`config.mk`, `ghc.cabal`, `ghc-bin.cabal`, ...}. 

* '''make''': do the rest of the build in three phases by invoking `ghc.mk` with the phase parameter set to one of {`0`, `1`, `final`}. All other `*.mk` files {`config.mk`, `tree.mk`, ...} are included in `ghc.mk`. The approximate build order is described in [source:ghc/ghc.mk ghc.mk]. 

The goal is to eventually replace all of the above with a single shake script that will be invoking `autoconf`, `configure`, etc., and taking care of all dependencies. Specific parts of the old build system that will be shake-ified are: `boot`, `ghc-cabal` and `*.mk`.

=== Why are we doing this? ===

We are unhappy with the current build system for a number of reasons including:
* It works in a number of phases, relating to when you can generate dependency information from files that are themselves generated. `Shake` is specifically designed to eliminate this complication. 
* For good reasons the current build systems uses `make` in a very tricky way; you can see expressions like `$$$$(dir $$$$@)`, meaning multiple layers of interpretation.
* Because `make` has a single global variable space, it is extremely hard to find how a particular variable gets its value.

== Naming conventions ==

We want to keep all existing naming conventions. For example, `*.o` files will stay as they are (as opposed to being renamed to `*.hs.o` and `*.c.o` as advised in shake documentation). This implies that there will be

* either a separate (automatically generated) build rule for each `*.o` file in the shake script, which may end up being slow;

* or the filenames will be stored in `Set`-like structures (e.g., `setHsObj` and `setCObj`) and the corresponding build rules will be: 
{{{
(`member` setHsObj) ?>  \out -> do { ... }.
}}}

== Build options ==

One of the major difficulties will be taking care of various build options. A possible direction to start with is pulling the configuration out of the `*.mk` files, with a ''parser'' for a subset of `Makefile`, resolve the variables, and then use that information to drive the `Shake` rules. Some initial inspiration can be taken from the `Shake.Config` module that can parse simple configuration files and is integrated with shake rules. Some configuration values can be passed as command line parameters to `Shake`, which can be handled using `shakeArgs`.

Parsing the existing `*.mk` files and extracting variables is an interesting small standalone project -- one that could be delegated out. (Not yet taken up by anyone: feel free to volunteer!).

== Intermediate goals ==

Following the divide-and-conquer principle, we split the big goal into a number of less ambitious ones below. More intermediate goals will be added here as the project progresses.

=== Shaking up a library ===

The first intermediate goal is to choose a library and build it with `Shake`. This will be tested by running the existing build system, removing all the built stuff for this particular library, and then restoring it with `Shake`, hopefully getting the same result. It was decided to choose a library without `cbits` and `#include`'s for the first attempt. (A particular library has not yet been chosen, feel free to make suggestions.)

== How to contribute ==

Please email to `andrey dot mokhov at ncl dot ac dot uk` who coordinates the efforts if you'd like to contribute or have any comments/suggestions.

```