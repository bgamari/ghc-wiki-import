CONVERSION ERROR

Original source:

```trac
[[PageOutline]]

= Shaking up GHC =

This page is a working document to capture the ongoing work on migrating the current GHC build system based on `make` into a new and (hopefully) better one based on `Shake`. See [wiki:Building/Architecture] and [wiki:Building/Using] for more details on the current build system. For general reference to `Shake`, the first sources to try are:
* http://hackage.haskell.org/package/shake/docs/Development-Shake.html (Haddock docs)
* https://github.com/ndmitchell/shake/blob/master/docs/Manual.md#readme (User manual)

Somewhat related, #5793 suggests using `Shake` for `nofib`, and there's some code attached as well: attachment:Main.2.hs:ticket:5793

== The goal ==

The existing build system performs the following major steps:

* '''boot''': run `autoconf` on {`configure.ac`, ...} to produce the `configure` script, as well as `libraries/*/GNUmakefile` and `libraries/*/ghc.mk` files. Note that some `libraries/*` also contain `configure.ac` and thus require `autoconf` to create local `configure` scripts.

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
(`member` setHsObj) ?> \out -> do { ... }.
}}}

== Build options ==

One of the major difficulties will be taking care of various build options. A possible direction to start with is pulling the configuration out of the `*.mk` files, with a ''parser'' for a subset of `Makefile`, resolve the variables, and then use that information to drive the `Shake` rules. Some initial inspiration can be taken from the [http://hackage.haskell.org/package/shake-0.13.4/docs/Development-Shake-Config.html Shake.Config module] that can parse simple configuration files and is integrated with shake rules. Also see [https://github.com/ndmitchell/shake/tree/master/Development/Make Development.Make] for a simple `Makefile` parser embedded in `Shake`. Some configuration values can be passed as command line parameters to `Shake`, which can be handled using `shakeArgs`.

Parsing the existing `*.mk` files and extracting variables is an interesting small standalone project (see intermediate goals).

=== Where options come from ===

The table below explains where most build variables are defined (this is taken from `rules/distdir-way-opts.mk`). Arguments `$1-$4` stand for:
* `$1` is the directory we're building in
* `$2` is the distdir (e.g. "dist", "dist-install" etc.)
* `$3` is the way (e.g. "v", "p", etc.)
* `$4` is the stage ("1", "2", "3")

||=  Variable =||= Purpose =||= Defined by^*^ =||
||`$1_PACKAGE`           || Package name for this dir, if it is a package || `$1/$2/ghc.mk` ||
||`CONF_HC_OPTS`           || GHC options from `./configure` || `mk/config.mk.in` ||
||`CONF_HC_OPTS_STAGE$4`   || GHC options from `./configure` specific to stage `$4` || `mk/config.mk.in` ||
||`WAY_$3_HC_OPTS`         || GHC options specific to way `$3` || `mk/ways.mk` ||
||`SRC_HC_OPTS`            || source-tree-wide GHC options || `mk/config.mk.in`, `mk/build.mk`, `mk/validate.mk` ||
||`SRC_HC_WARNING_OPTS`    || source-tree-wide GHC warning options || `mk/config.mk.in`, `mk/build.mk`, `mk/validate.mk` ||
||`EXTRA_HC_OPTS`          || for supplying extra options on the command line || `make EXTRA_HC_OPTS=...` ||
||`$1_HC_OPTS`             || GHC options specific to dir `$1` || `$1/$2/package-data.mk` ||
||`$1_$2_HC_OPTS`          || GHC options specific to dir `$1` and distdir `$2` || `$1/$2/package-data.mk` ||
||`$1_$2_$3_HC_OPTS`       || GHC options specific to dir `$1`, distdir `$2` and way `$3` || `$1/$2/package-data.mk` ||
||`$1_$2_MORE_HC_OPTS`     || GHC options specific to dir `$1` and distdir `$2` || ?? ||
||`$1_$2_EXTRA_HC_OPTS`    || GHC options specific to dir `$1` and distdir `$2` || `mk/build.mk` ||
||`$1_$2_HC_PKGCONF`       || `-package-db` flag if necessary || `rules/package-config.mk` ||
||`$1_$2_HS_SRC_DIRS`      || dirs relative to `$1` containing source files || `$1/$2/package-data.mk` ||
||`$1_$2_CPP_OPTS`         || CPP options || `$1/$2/package-data.mk` ||
||`<file>_HC_OPTS`         || GHC options for this source file (without the extension) || `$1/$2/ghc.mk` ||

^*^ Note: this now appears to be outdated -- some variable definitions have been moved to other files.
                          
== Intermediate goals ==

Following the divide-and-conquer principle, we split the big goal into a number of less ambitious ones below. More intermediate goals will be added here as the project progresses.

=== Mining variables ===

A parser for (a subset of) makefiles is being implemented in order to mine all variable definitions and associated conditions from the existing makefiles (876 makefiles found in the entire GHC tree). The definitions are to be further converted to Haskell code in a semi-automated way.

System related `@variables@` which are expanded by `configure` are to be placed in `default.config.in` file that will be processed by `configure` to produce `default.config` file that will be read by the `Shake` build system. GHC developers can override some of the default settings using the `user.config` file, whose role will correspond to that of `build.mk` in the current build system.

=== Shaking up a library ===

The first intermediate goal is to choose a library and build it with `Shake`. This will be tested by running the existing build system, removing all the built stuff for this particular library, and then restoring it with `Shake`, hopefully getting the same result. It was decided to choose a library without `cbits` and `#include`'s for the first attempt; `libraries/haskell2010` seems like a good candidate. The build code should be sufficiently generic to handle all other libraries without much rewriting.

== Notes on configuration ==

In discussions we came up with a plan for dealing with configuration options, that will probably be delayed until after the first version is produced. The plan is to introduce the types approximately like:

{{{
data Config = Config {compileProfiling :: Bool, enableRTSDebugging :: Bool, intLibrary :: IntLibrary, extraSettings :: [Setting]}

data Setting = Setting {builder :: Builder, file :: Maybe FilePath, package :: Maybe FilePath, disable :: Bool, args :: [String]}
}}}

Users who want a custom configuration will write a Config.hs file such as:

{{{
module UserConfig where

import GhcBuild.Config

userConfig :: Config -> Config
userConfig c = (crossCompile $ fastBuild c){intLibrary = IntInteger2}
}}}

All rules currently in the Makefile's will be written in a module inside the build system such as:

{{{
module BuildConfig where

buildConfig :: Config -> [Setting]
buildConfig Config{..} =
    [Setting (GHC Stage2) (Just "RTS.hs") Nothing False ["-DINTEGER2"] | intLibrary == IntInteger2] ++
    extraSettings
}}}

The setting information would be cached in an oracle and matched on when supplying arguments to builders.

== How to contribute ==

Please email to `andrey.mokhov@ncl.ac.uk` who coordinates the efforts if you'd like to contribute or have any comments/suggestions.

```
