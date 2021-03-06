# Profiling GHC itself



If GHC itself is running too slowly, you can profile the compiler itself.  The way to do this is to add


```wiki
GhcProfiled=YES 
```


to your `build.mk` file.  This is more robust than trying things like `GhcStage2HcOpts += -prof` because there are several things to do: first we build the ghc library, then we build the ghc program, linked against the library.



You can also use the prof BuildFlavor in the default `build.mk`:


```wiki
# Profile the stage2 compiler:
BuildFlavour = prof
```


Once you've done this, you should be able to run GHC (stage 2) to generate time and space profiles. For example:


```wiki
$(TOP)/inplace/bin/ghc-stage2 +RTS -p -RTS
```


Note that this builds a profiled *stage-2* compiler.  In principle it's possible to build a profiled *stage-1* compiler, but the build system isn't set up to do that right now.  Notably, various libraries (eg Cabal) are built and installed by the bootstrap compiler before building GHC; these would need to be built and installed in a profiled way too. Additionally, the built compiler will manifest any profiling bugs that were in your bootstrapping compiler.



If you want to profile GHC while compiling GHC, the easiest way to do this is to build a *stage-3* compiler with your profiled *stage-2* compiler. You’ll need to run `make stage=3` the first time you do this in order to build the dependencies for the stage3 compiler; see [Rebuilding GHC](building/using#rebuilding-the-ghc-binary-after-making-changes) and below for more details.



Setting `GhcProfiled` also enables profiled Haddock, which can be built by running `make HADDOCK_DOCS=yes`. This is useful if you're investigating a regression which is showing up from a Haddock performance test.


## Tracking down regressions



While it is useful to occasionally profile GHC and look for hotspots which need optimizing, a more likely situation is that one of our performance tests has informed you that some metric which we are tracking has gone up. Don't just blindly bump the number up: some time spent investigating now will save us a lot of performance debugging later!  The best thing to do is to bisect the commit where the performance changed, and build both before and after with profiling enabled.



Here are some tips and tricks:


- Check out [
  http://perf.haskell.org/ghc](http://perf.haskell.org/ghc), which may have already bisected the problem for you.

- See [Bisection](working-conventions/bisection) for tips on bisection.

- When comparing the profiles of multiple versions of GHC, you may find yourself needing to maintain multiple build trees with different code. An extremely useful utility for this case is `git-new-workdir`, which is not a default Git command but lives in the `contrib` folder of Git. You can `locate git-new-workdir` to find your copy; it is most commonly installed to `/usr/share/doc/git/contrib/workdir` and add it to your path.  Now you can use this command (`git-new-workdir old-working-copy new-working-copy`) to create a new working directory from the old Git repository, that has its own index but shares a repository with the original. Be sure to switch to a different branch, since branch pointers are shared too! What is especially convenient is if you make and commit a temporary change in one working-copy, you can immediately cherry-pick it from the other one, no fetching or patches necessary!

- One important thing to note is that by default, GHC does not define very many cost centers. So if you are comparing the results of two profiles, you may notice that everything gets attributed to something not very informative (e.g. `defaultCleanupHandler`). This is by design: if we slapped `-auto-all` on all of our source files, the profiles would be a lot harder to interpret because there would be so much noise. Instead, you should selectively apply `-auto-all`, probably to the files changed in the offending commit. [compiler/ghc.mk](/trac/ghc/browser/ghc/compiler/ghc.mk) has some guidance on the matter: you can either set `compiler/main/GhcMake_HC_OPTS` to `-auto-all`, or you can add `{-# OPTIONS_GHC -auto-all #-}` to the top of the relevant files. The latter has the benefit that it will also properly trigger recompilation; however, if you're sharing your ``build.mk`` between the old and the new trees, adding the appropriate `HC_OPTS` to your build files prevents you from having to duplicate changes in both trees. These variables can also be passed to `make` on the command line.

- Comparing time/allocation profiles from `-p` can be a bit of a pain, especially if you're tracking a minor regression. It would be nice if there was a tool for comparing profiles. [\#9419](https://gitlab.staging.haskell.org/ghc/ghc/issues/9419) tracks a feature request for adding a machine-parseable version of this output.

- If your commit updated submodules, be sure that you didn't also pull in unrelated changes when the submodule was updated. They may be the culprit, so test them seperately!

- `max_bytes_used` can be a delicate thing to measure against, since it is often sensitive to whether or not a major GC occurs. If you see a change like this, check the GC statistics and see if the number of major GCs varies

- Don't forget to set `stage=2` so you're not repeatedly rebuilding the stage 1 compiler.

## Re-running Haddock perf benchmarks



One interesting idiosyncracy of GHC validate is how the Haddock performance tests (found in [testsuite/tests/perf/haddock](/trac/ghc/browser/ghc/testsuite/tests/perf/haddock)) are setup. In particular, they do \*not\* actually go ahead and run Haddock; instead, they read out the file `base.haddock.t` (and variants) which gets written out with garbage collection statistics during the build process itself. How, then, might one go about re-running the tests? The answer is that the files themselves contain the command line which was used to make the invocation.  Thus, one workflow might proceed as follows:


1. Do a normal build of the GHC tree with `HADDOCK_DOCS = YES`
1. Look at `libraries/base/dist-install/doc/html/base/base.haddock.t`, which now contains a giant command line. Copy this line into a shell script file.
1. Make this command line actually runnable. In my experience, this requires that you (1) replace `inplace/lib/bin/haddock` with `inplace/bin/haddock` (so that proper environment variables are initialized), (2) fix any quoting problems with the arguments (the `--title` flag is the usual culprit), and (3) replace the `+RTS` options at the very end with `$@`, so you can profile the way you please.


To rebuild haddock, it is best to `rm -Rf utils/haddock/dist` rather than run `make clean` which tends to be a bit overzealous.


