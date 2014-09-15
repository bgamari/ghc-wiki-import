# GHC Status Report, October 2014



GHC development is as busy as ever, and 7.10 is going to be no different. Our current release schedule looks like **we'd like an RC by Christmas**, and a **release close to Feburary 2015**, which is something of an adjustment based on the 7.8 release. But we've still got plenty planed for all our users, as usual.



However, we still need help with it all. GHC is a community project, and as you may be aware, most of this is done by our wonderful contributors. If you want something done, you should certainly try to get in touch and help make it a reality.


## Plans for 7.10


## Libraries, source language, type system


- **Applicative is now a superclass of Monad**. TODO Austin.

- **Signature sections**.  Lennart Augustsson is implementing `(:: ty)` to work the same as `(\x->x:;ty)`.  Needs a wiki design page.

- **[ApplicativeDo](applicative-do)** - Now that `Applicative` is a superclass of `Monad`, Simon Marlow has plans to implement a new extension for GHC, which will allow `do` notation to be used in the context of `Applicative`, not just `Monad`.

- **Strict language extension**.  Johan Tibell is working on a `-XStrict` language extension that will make GHC compile programs in a by-default strict way.  [Details here](language-strict).

- **Cloud Haskell statics**.  Mathieu Boespflug and Facundo Domínguez at TweagIO are working on support for Cloud Haskell's `static` feature.  [Details here](static-pointers). The current in-progress code review is available at [
  https://phabricator.haskell.org/D119](https://phabricator.haskell.org/D119)

- **Overloaded record fields** - In 2013, Adam Gundry implemented the new `-XOverloadedRecordFields` extension for GHC, described on the wiki [OverloadedRecordFields](overloaded-record-fields). We're still aiming to make this part of 7.10, but there's still work to be done!

- **Using an SMT Solver in the type-checker** - Iavor Diatchki is working on utilizing an off-the-shelf SMT solver in GHC's constraint solver. Currently, the main focus for this is improved support for reasoning with type-level natural numbers, but it opens the doors to other interesting functionality, such as supported for lifted (i.e., type-level) `(&&)`, and `(||)`, type-level bit-vectors (perhaps this could be used to implement type-level sets of fixed size), and others.   This work is happening on branch `wip/ext-solver`.

- **Kind equality and kind coercions** - Richard Eisenberg (with support from Simon PJ and Stephanie Weirich, among others) is implementing a change to the Core language, as described in a recent paper [
  FC](http://www.seas.upenn.edu/~eir/papers/2013/fckinds/fckinds-extended.pdf). When this work is complete, *all* types will be promotable to kinds, and *all* data constructors will be promotable to types. This will include promoting type synonyms and type families. As the details come together, there may be other source language effects, such as the ability to make kind variables explicit. It is not expected for this to be a breaking change -- the change should allow strictly more programs to be accepted.

- **Partial type signatures** - Thomas Winant and Dominique Devriese are working on partial type signatures for GHC. A partial type signature is a type signature that can contain *wildcards*, written as underscores. These wildcards can be types unknown to the programmer or types he doesn't care to annotate. The type checker will use the annotated parts of the partial type signature to type check the program, and infer the types for the wildcards. A wildcard can also occur at the end of the constraints part of a type signature, which indicates that an arbitrary number of extra constraints may be inferred. Whereas `-XTypedHoles` allow holes in your terms, `-XPartialTypeSignatures` allow holes in your types. The design as well as a working implementation are currently being simplified [PartialTypeSignatures](partial-type-signatures).

## Back-end and runtime system


- **CPU-specific optimizations** - Austin is currently investigating the implementation of CPU-specific optimisations for GHC, including new `-march` and `-mcpu` flags to adjust tuning for a particular processor. Right now, there is some preliminary work towards optimizing copies on later Intel machines. There's interest in expanding this further as well.

- **Changes to static closures for faster garbage collection** - Edward is working on an overhaul of how static closures represented at runtime to eliminate some expensive memory dereferences in the GC hotpath. The initial results are encouraging: these changes can result in an up to 8% in the runtime of some GC heavy benchmarks, see [\#8199](https://gitlab.staging.haskell.org/ghc/ghc/issues/8199).

- **New, smaller array type** - Johan Tibell has added a new array type, `SmallArray#`, which uses less memory (2 words) than the `Array#` type, at the cost of being more expensive to garbage collect for array sizes larger than 128 elements.

- **Faster small array allocation** - Johan Tibell has made array allocation of arrays of small, statically know size faster by making it inline with the normal heap check, instead of out-of-line in a separate nursery block.

- **DWARF-based stack tracing** - Peter Wortmann and Arash Rouhani (with support from the Simons) are working on enabling GHC to generate and use DWARF debugging information. This should allow us to obtain stack traces and do profiling without the need for instrumentation. The first stages of this work should land in 7.10, but it's not clear if the full feature set will.

- **Reimplemented GMP-based `Integer` backend ([\#9281](https://gitlab.staging.haskell.org/ghc/ghc/issues/9281))** - This provides a GMP-based `Integer` backend not relying on registering GHC-specific [
  custom GMP memory allocators](https://gmplib.org/manual/Custom-Allocation.html) which cause problems when linking to other C-code also using GMP unaware of GHC's memory management.

## Frontend, build-system, and miscellaneous changes


- **GHC is now using Submodules for all repositories**. TODO

- **Phabricator for code review**. TODO

# References



\[AMP\] [
https://github.com/quchen/articles/blob/master/applicative\_monad.md](https://github.com/quchen/articles/blob/master/applicative_monad.md) 
 
\[ORF\] [
https://ghc.haskell.org/trac/ghc/wiki/Records/OverloadedRecordFields](https://ghc.haskell.org/trac/ghc/wiki/Records/OverloadedRecordFields) 

\[FC\] System FC with Explicit Kind Equality - [
http://www.seas.upenn.edu/\~eir/papers/2013/fckinds/fckinds-extended.pdf](http://www.seas.upenn.edu/~eir/papers/2013/fckinds/fckinds-extended.pdf) 

\[PTS\] [
https://ghc.haskell.org/trac/ghc/wiki/PartialTypeSignatures](https://ghc.haskell.org/trac/ghc/wiki/PartialTypeSignatures) 

\[HEAPALLOCED\] [
https://ghc.haskell.org/trac/ghc/ticket/8199](https://ghc.haskell.org/trac/ghc/ticket/8199) 

