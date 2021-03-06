CONVERSION ERROR

Original source:

```trac
= Setting up a Solaris system for building GHC =

These instructions have only been checked for GHC 6.12.1 on Solaris 10 on SPARC. They should also apply to later versions of GHC, Solaris 8 and later, and perhaps Solaris on x86. 

GHC versions 6.10.1 and earlier don't have a working SPARC native code generator, and have many small build issues with Solaris. Use GHC 6.12.1 or later.

== Installing GNU packages ==

GHC relies on many GNU-isms that are not supported by the native Solaris build tools. The following environment is known to work. Later versions may work but have not been tested. Taking the time to install these tools is likely to be less painful than debugging build problems due to unsupported versions (and this is your official warning).

 || GNU binutils 2.20  || for GNU ld, maybe others ||
 || GNU coreutils 8.4  || for GNU tr, maybe others ||
 || GNU make 3.81     || make files use GNU extensions ||
 || GNU m4 1.4.13     || ||
 || GNU sed 4.2           || build scripts use GNU extensions ||
 || GNU tar 1.20         || Solaris tar doesn't handle large file names ||
 || GNU grep 2.5      || build scripts use GNU extensions ||
 || GNU readline 5 || ||
 || GNU ncurses 5.5 || ||
 || Python 2.6.4 || needed to run the testsuite with multiple threads ||
 || GCC 4.1.2       || this exact version is strongly recommended (3.4.3 is known to not work, see #8829) ||

Some of these can be obtained as binary versions from the  [http://www.blastwave.org/ blastwave.org] collection, others need to be downloaded as source from [http://www.gnu.org gnu.org].

The blastwave libraries are usually installed under `/opt/csw`, so you may need to manually set `LD_LIBRARY_PATH` to point to them:

{{{
export LD_LIBRARY_PATH=/opt/csw/lib
}}}

== Using a bootstrapping GHC ==

You can either get a binary distribution from the GHC download page or use some other pre-existing GHC binary. These binaries usually assume that required libraries are reachable via LD_LIBRARY_PATH, or are in `/opt/csw`. If you get errors about missing libraries or header files, then the easiest solution is to create soft links to them in, `lib/ghc-6.12.1` and `lib/ghc-6.12.1/include` of the installed binary distribution. These paths are always searched for libraries / headers.

```
