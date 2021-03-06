# Deterministic builds


## Motivation



Given the same inputs (source files and flags), GHC does not produce deterministic outputs.  This causes a number of problems:


- Gratuitous recompilation.  Suppose you edit a module in the middle of a project, changing only a comment.  GHC recompiles the module, producing a result that differs from the previous version, and then recompilation proceeds up the tree forcing many more modules to be recompiled.  This is an extreme example, but it happens on a smaller scale all the time.  The effect is much worse when optimisation is on, because gratuitous changes in unfoldings and other cross-module optimisation properties are more likely.
- Problems for third-party build and packaging systems such as Nix and Debian (see [\#4012](https://gitlab.staging.haskell.org/ghc/ghc/issues/4012)).  For example in Debian, if a package changes its hash, everything that depends on it (transitively) needs to be recompiled.  GHC's non-determinism means that simply recompiling a package can change its hash; so this forces a lot of unnecessary recompiling of packages.
- Problems for build systems that cache build outputs, which assume that compilation is deterministic.  Build systems that assume a given set of inputs will produce the same (or a compatible) output don't work with GHC. 

## Goal



Given the same 


- GHC (see below)
- source files
- flags (excluding --make, -j, and debugging flags)
- installed packages


GHC should always produce the same interface files.



In particular we're not aiming for bit-for-bit identical object files (at least initially).  Identical interface files implies ABI compatibility, and ABI compatibility implies that the object files are, if not identical, at least compatible, since the ABI describes everything that an external client knows about the object file.  ABI compatibility addresses all the points in the motivation.



What *can* change, and still get identical output?  (A non-exhaustive list.)


- The contents of the file system (eg `/tmp`)
- Old interface files


For example: 


- compile the package 
- change `Foo.hs` 
- recompile
- undo the change
- recompile


The final step should have identical output to the first.


## Scope



What do we mean by "the same GHC"? Can we recompile GHC with different optimisation flags, or with profiling?  



For most purposes, e.g. those in Motivation above, "the very same GHC binary" is an acceptable definition of "the same GHC".  However, consider what happens when we rebuild GHC itself in a GHC source tree - suppose we rebuild stage 1 in a way that only changes a comment, or only changes an optimisation setting, and then we recompile a library module. Should it produce the same output?  It would be strange if it didn't, and it would lead to a \*lot\* of recompilation when developing GHC.  Currently GHC is rarely this non-deterministic, and there's no reason it should be.  But it's hard to nail down exactly what this definition should be.



For the sake of having a concrete definition, let's use "built from the same sources with the same flags, excluding optimisation and profiling flags".  This doesn't capture everything, but it's good enough.


## A concrete example


```wiki
$ ghc --version
The Glorious Glasgow Haskell Compilation System, version 7.11.20150915
# I'm using HEAD and 7.10.2 has the same problems
$ git clone http://github.com/haskell/pretty.git
Cloning into 'pretty'...
remote: Counting objects: 10363, done.
remote: Total 10363 (delta 0), reused 0 (delta 0), pack-reused 10363
Receiving objects: 100% (10363/10363), 5.04 MiB, done.
Resolving deltas: 100% (5795/5795), done.
$ cd pretty
$ cabal sandbox init
Writing a default package environment file to
/data/users/bnitka/pretty/cabal.sandbox.config
Creating a new sandbox at /data/users/bnitka/pretty/.cabal-sandbox
$ git checkout -b some-source-file-change 2ce46a57385ef480ba0777d790ed5ed554d4544b
Switched to a new branch 'some-source-file-change'
$ cabal build
Package has never been configured. Configuring with default flags. If this
fails, please run configure manually.
Warning: The package list for 'hackage.haskell.org' is 28.0 days old.
Run 'cabal update' to get the latest list of available packages.
Resolving dependencies...
Configuring pretty-1.1.3.1...
Building pretty-1.1.3.1...
Preprocessing library pretty-1.1.3.1...
[1 of 6] Compiling Text.PrettyPrint.Annotated.HughesPJ ( src/Text/PrettyPrint/Annotated/HughesPJ.hs, dist/build/Text/PrettyPrint/Annotated/HughesPJ.o )
[2 of 6] Compiling Text.PrettyPrint.Annotated.HughesPJClass ( src/Text/PrettyPrint/Annotated/HughesPJClass.hs, dist/build/Text/PrettyPrint/Annotated/HughesPJClass.o )
[3 of 6] Compiling Text.PrettyPrint.Annotated ( src/Text/PrettyPrint/Annotated.hs, dist/build/Text/PrettyPrint/Annotated.o )
[4 of 6] Compiling Text.PrettyPrint.HughesPJ ( src/Text/PrettyPrint/HughesPJ.hs, dist/build/Text/PrettyPrint/HughesPJ.o )
[5 of 6] Compiling Text.PrettyPrint.HughesPJClass ( src/Text/PrettyPrint/HughesPJClass.hs, dist/build/Text/PrettyPrint/HughesPJClass.o )
[6 of 6] Compiling Text.PrettyPrint ( src/Text/PrettyPrint.hs, dist/build/Text/PrettyPrint.o )
In-place registering pretty-1.1.3.1...
$ for i in $(find dist/ | grep \.hi); do echo $i; ghc --show-iface $i | md5sum; done
dist/build/Text/PrettyPrint/Annotated/HughesPJ.hi
6dd20478ebd71f5e57b203adfa53b750  -
dist/build/Text/PrettyPrint/Annotated/HughesPJClass.hi
992914f5b7fbeec8e05fe3ef14f8901d  -
dist/build/Text/PrettyPrint/Annotated.hi
33326048d7c0c2285cab7b2b7fa66261  -
dist/build/Text/PrettyPrint/HughesPJ.hi
5a8ba7a564a8e0bf3accfe733bc121df  -
dist/build/Text/PrettyPrint/HughesPJClass.hi
2de926deed2ad05fc3789e8671a0e510  -
dist/build/Text/PrettyPrint.hi
12d5132a2675af831120d386181c5d41  -
$ git checkout HEAD^ . # checkout an earlier version
$ cabal build
./pretty.cabal has been changed. Re-configuring with most recently used
options. If this fails, please run configure manually.
Warning: The package list for 'hackage.haskell.org' is 28.0 days old.
Run 'cabal update' to get the latest list of available packages.
Resolving dependencies...
Configuring pretty-1.1.3.1...
Building pretty-1.1.3.1...
Preprocessing library pretty-1.1.3.1...
[1 of 6] Compiling Text.PrettyPrint.Annotated.HughesPJ ( src/Text/PrettyPrint/Annotated/HughesPJ.hs, dist/build/Text/PrettyPrint/Annotated/HughesPJ.o )
[2 of 6] Compiling Text.PrettyPrint.Annotated.HughesPJClass ( src/Text/PrettyPrint/Annotated/HughesPJClass.hs, dist/build/Text/PrettyPrint/Annotated/HughesPJClass.o )
[3 of 6] Compiling Text.PrettyPrint.Annotated ( src/Text/PrettyPrint/Annotated.hs, dist/build/Text/PrettyPrint/Annotated.o )
[4 of 6] Compiling Text.PrettyPrint.HughesPJ ( src/Text/PrettyPrint/HughesPJ.hs, dist/build/Text/PrettyPrint/HughesPJ.o )
[5 of 6] Compiling Text.PrettyPrint.HughesPJClass ( src/Text/PrettyPrint/HughesPJClass.hs, dist/build/Text/PrettyPrint/HughesPJClass.o )
[6 of 6] Compiling Text.PrettyPrint ( src/Text/PrettyPrint.hs, dist/build/Text/PrettyPrint.o )
In-place registering pretty-1.1.3.1...
$ git checkout HEAD . # go back
$ cabal build
./pretty.cabal has been changed. Re-configuring with most recently used
options. If this fails, please run configure manually.
Warning: The package list for 'hackage.haskell.org' is 28.0 days old.
Run 'cabal update' to get the latest list of available packages.
Resolving dependencies...
Configuring pretty-1.1.3.1...
Building pretty-1.1.3.1...
Preprocessing library pretty-1.1.3.1...
[1 of 6] Compiling Text.PrettyPrint.Annotated.HughesPJ ( src/Text/PrettyPrint/Annotated/HughesPJ.hs, dist/build/Text/PrettyPrint/Annotated/HughesPJ.o )
[2 of 6] Compiling Text.PrettyPrint.Annotated.HughesPJClass ( src/Text/PrettyPrint/Annotated/HughesPJClass.hs, dist/build/Text/PrettyPrint/Annotated/HughesPJClass.o )
[3 of 6] Compiling Text.PrettyPrint.Annotated ( src/Text/PrettyPrint/Annotated.hs, dist/build/Text/PrettyPrint/Annotated.o )
[4 of 6] Compiling Text.PrettyPrint.HughesPJ ( src/Text/PrettyPrint/HughesPJ.hs, dist/build/Text/PrettyPrint/HughesPJ.o )
[5 of 6] Compiling Text.PrettyPrint.HughesPJClass ( src/Text/PrettyPrint/HughesPJClass.hs, dist/build/Text/PrettyPrint/HughesPJClass.o )
[6 of 6] Compiling Text.PrettyPrint ( src/Text/PrettyPrint.hs, dist/build/Text/PrettyPrint.o )
In-place registering pretty-1.1.3.1...
$ for i in $(find dist/ | grep \.hi); do echo $i; ghc --show-iface $i | md5sum; done # look at the hashes again
dist/build/Text/PrettyPrint/Annotated/HughesPJ.hi
9529ab766d3a3cc31fb406b10936c01d  -
dist/build/Text/PrettyPrint/Annotated/HughesPJClass.hi
ad9fa48557707dfb3bc6e62565f4f55c  -
dist/build/Text/PrettyPrint/Annotated.hi
7dfdb86e28f7386555b615793434df55  -
dist/build/Text/PrettyPrint/HughesPJ.hi
bcc8cb188cf0cb7354485db3178473df  -
dist/build/Text/PrettyPrint/HughesPJClass.hi
38aec999daf02c393a0925740d6f46ac  -
dist/build/Text/PrettyPrint.hi
a445878a7cec5681355a62b238e7cd00  -
# the hashes are different but the environment didn't change
```

## Known sources of nondeterminism


### Nondeterministic Uniques



The order of allocated Uniques is not stable across rebuilds. The main reason for that is that type-checking interface files pulls 
Uniques from UniqSupply and the interface file for the module being currently compiled can, but doesn't have to exist.
It gets more complicated if you take into account that the interface files are loaded lazily and that building multiple files at once has to
work for any subset of interface files present. When you add parallelism this makes Uniques hopelessly random.


### cabal sometimes running alex, happy



This is not a problem with GHC, but comes up when compiling some packages that have `Lexer.x` and `Lexer.hs` files. 
`Cabal` will sometimes run `alex Lexer.x` to regenerate `Lexer.hs` file. If the versions of `alex` are different you can get different sources.
See [ \#2311](https://github.com/haskell/cabal/issues/2311), [
\#2940](https://github.com/haskell/cabal/issues/2940).


### cabal sometimes finding cpphs or c2hs from the sandbox



When building multiple packages in a sandbox in parallel including `cpphs`, depending on the order the packages get compiled `cabal` sometimes
finds `cpphs` and runs it, leading to a slightly different results.


## Progress



Current work is focused on making GHC independent of the order of Uniques. That means either removing the call sites of functions that introduce ordering based on Uniques or 
when the end result is deterministic, documenting it and making sure it stays local. The main sources of non-determinism are: `Ord Unique`, `foldUFM`, `elemsUFM`, `ufmToList`, `keysUFM`.



I've mapped out some of them [
here (warning: 3 MB png file)](https://raw.githubusercontent.com/niteria/notes/master/Deterministic%20builds%20mind%20map.png). 
Green means done, orange means that it still needs work, red means that the path was abandoned. It's not really meant to be read by anyone else but me, but might give some sense of what still needs to be done.
  


## Testing



Two new flags were added to GHC:


- `-dinitial-unique`
- `-dunique-increment`


By varying these you can get interesting effects:


- `-dinitial-unique=0 -dunique-increment=1` - current sequential UniqSupply

- `-dinitial-unique=16777215 -dunique-increment=-1` - UniqSupply that generates in decreasing order

- `-dinitial-unique=1 -dunique-increment=PRIME` - where PRIME big enough to overflow often, eg. `8338881` - nonsequential order

## Deterministic multithreaded builds



At this moment we don't need it, but judging by the comments on [\#4012](https://gitlab.staging.haskell.org/ghc/ghc/issues/4012) some people would and we might in the future.
To the best of my knowledge it just works.


## Previous discussions



[
https://mail.haskell.org/pipermail/ghc-devs/2015-September/009964.html](https://mail.haskell.org/pipermail/ghc-devs/2015-September/009964.html)



[\#4012](https://gitlab.staging.haskell.org/ghc/ghc/issues/4012)



[\#12262](https://gitlab.staging.haskell.org/ghc/ghc/issues/12262), [\#12935](https://gitlab.staging.haskell.org/ghc/ghc/issues/12935) - for bit-for-bit determinism


