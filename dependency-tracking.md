CONVERSION ERROR

Original source:

```trac
= Improved dependency tracking for GHC =

Many packages these days are using Template Haskell to grab information from the filesystem at compile time. I'll use Hamlet as a motivating example, though this applies to others as well.

In Hamlet, you can specify an external file as the source for your HTML template code, with something like:
{{{
{-# LANGUAGE TemplateHaskell #-}
module M where
  import X
  import Y
  $(renderHamlet $(hamletFile "myfile.hamlet"))
}}}
Now suppose "myfile.hamlet" is updated, but there are no changes in `M.hs`, `X.hs`, `Y.hs` (and their imports).  Then `ghc --make` will think that `M` doesn't need recompiling at all and will not recompile `M` (possibly saying "Recompilation is NOT required"). Which is obviously wrong.  

Even with plain module-at-a-time `ghc -c M.hs`, GHC may wrongly fail to recompile M because it thinks its unnecessary. You have to use `-fforce-recomp` to make it happen.

The situation is very similar with CPP:
{{{
{-# LANGUAGE Cpp #-}
module M where
  import X
  import Y
  #include "myfile.h"
}}}
Again `ghc --make` will fail to see the dependency on "myfile.h" and may therefore fail to recompile `M` when it should really do so.

== Possible solution ==

The solution comes in these pieces:
 1. A part (different for TH and CPP) that tells GHC what files went into compiling M
 2. A way (shared) to record this "file-dependency info" in M's interface file, `M.hi`
 3. A new bit of the recompilation check that makes M recompile if any of its file-dependencies are out of date.

Concering (2), GHC already has a components of M.hi that records "usages": fingerprints of the things that GHC used in compiling M.  This is the `mi_usages` field of `HscTypes.ModIface`.  To record the extra dependency we can just add a new constructor to `HscTypes.Usage` for `FileUsage`.  (Record an absolute, not relative path!)

Then (3) is relatively easy: just check the modification date of `M.o` against the dependened-on file.

That leaves (1).  We'll need:

 * A new field in `TcgGblEnv` to accumulate file-dependencies.

 * Some way for `#include` directives to be recorded in that list (presumably by interpreting the #line directives that CPP outputs.

 * A way for Template Haskell code to express a dependency.  A [http://www.reddit.com/r/haskell/comments/k4lc4/yesod_the_limitations_of_haskell/c2hipo3 possible solution] is to add a new function to the template-haskell package:

    {{{addDependentFile :: FilePath -> Q ()}}}

 This in turn would require the `Qusai` class to support such an operation.

'''Note''' that `ghc -M` would not spit out these dependencies.  The solution proposed here simply records what dependencies where encountered ''while compiling M'', and hence when M should be re-compiled. It doesn't tell you (in advance) which files compiling M ''will'' depend on. 
```
