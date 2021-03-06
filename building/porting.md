


# Porting GHC to a new platform



(**This is no longer supported. See [CrossCompilation](cross-compilation) instead**)



This section describes how to port GHC to a currently
unsupported platform.  To avoid confusion, when we say
"architecture" we are referring to the processor, and
we use the term "platform" to refer to the combination
of architecture and operating system.



The first step in porting to a new platform is to get an
*unregisterised* build working.  An unregisterised build is one that
compiles via vanilla C only. This costs about a factor of two in
performance, but since unregisterised compilation is usually just a step
on the way to a full registerised port, we don't mind too much.



You should go through this process even if your architecture already
has registerised support in GHC, but your OS currently isn't supported.
In this case you probably won't need to port any of the
architecture-specific parts of the code, and you can proceed straight
from the unregisterised build to build a registerised compiler.



Notes on GHC portability in general: we've tried to stick
to writing portable code in most parts of the system, so it
should compile on any POSIXish system with gcc, but in our
experience most systems differ from the standards in one way or
another.  Deal with any problems as they arise - if you get
stuck, ask the experts on glasgow-haskell-users@….



Lots of useful information about the innards of GHC is available in
the [Commentary](commentary), which might be helpful if you run into some
code which needs tweaking for your system.


## Cross-compiling to produce an unregisterised GHC



**NOTE**: Versions supported: 6.11+.



**NOTE**: for current issues, see 

<table><tr><th>[\#3472](https://gitlab.staging.haskell.org/ghc/ghc/issues/3472)</th>
<td>Porting through .hc files broken</td></tr></table>




In this section, we explain how to bootstrap GHC on a new platform,
using unregisterised intermediate C files.  We haven't put a great
deal of effort into automating this process, for two reasons: it is
done very rarely, and the process usually requires human intervention
to cope with minor porting issues anyway.



The following step-by-step instructions should result in a fully
working, albeit unregisterised, GHC.  Firstly, you need a machine that
already has a working GHC (we'll call this the *host* machine), in
order to cross-compile the intermediate C files that we will use to
bootstrap the compiler on the *target* machine. We'll assume that you
are porting to platform *plat*, e.g. *plat* may be
`x86_64-unknown-linux`.



**NOTE**: the path on the host and the target should be the same.



**On the target machine**



Unpack a source tree (preferably a released
version).  We will call the path to the root of this
tree `<T>`.  



In the instructions that follow, "`<T>$ cmd`" means that the current directory should be `<T>` when executing the command "`cmd`".



If your target platform requires it, then you may need to set CFLAGS appropriately here, e.g.


```wiki
$ export CFLAGS=-m64
```


Now begin with:


```wiki
<T>$ cp /bin/pwd utils/ghc-pwd/ghc-pwd
<T>$ perl boot
<T>$ ./configure --enable-hc-boot --build=plat --host=plat --target=plat
```


You might need to update `configure.ac` to recognise the new
platform, and re-generate `configure` with `autoreconf`.



If necessary on your platform, you may again need to create a mk/build.mk
to pass any necessary flags to gcc, e.g.:


```wiki
SRC_CC_OPTS += -m64
```


As of June 23rd, 2009, GHC HQ has exorcised and split out GMP from the runtime
system into the separate 'integer-gmp' package. If you are bootstrapping a
compiler and are going to use integer-gmp for your `Integer` type, instead of
'integer-simple' which is a pure Haskell equivalent, then you will need to
run `configure` in `libraries/integer-gmp` here as well:


```wiki
<T>$ cd libraries/integer-gmp
<T>$ ./configure
<T>$ cd ../..
```


ToDo: explain how to use the 'integer-simple' package.



Then:


```wiki
<T>$ make bootstrapping-files
```


**On the host machine**



Unpack a source tree (exactly the same version as before).  Call this directory `<H>`.


```wiki
<H>$ perl boot
<H>$ ./configure --target=plat
```


Create `<H>/mk/build.mk`, with the following contents:


```wiki
PORTING_HOST = YES
GhcUnregisterised = YES
GhcLibHcOpts = -O -fvia-C -keep-hc-files
GhcRtsHcOpts = -keep-hc-files
GhcLibWays = v
GhcRTSWays =
SplitObjs = NO
GhcWithNativeCodeGen = NO
GhcWithInterpreter = NO
GhcStage1HcOpts = -O
GhcStage2HcOpts = -O -fvia-C -keep-hc-files
SRC_HC_OPTS += -H32m
GhcWithSMP = NO
utils/ghc-pkg_dist-install_v_HC_OPTS += -keep-hc-files
```


Edit `<H>/mk/project.mk`:


- copy `LeadingUnderscore` setting from target.


Copy `<T>/includes/ghcautoconf.h`,
`<T>/includes/dist-derivedconstants`, and
`<T>/includes/dist-ghcconstants` to `<H>/includes`.
Note that we are building on the host machine, using the
target machine's configuration files.  This
is so that the intermediate C files generated here will
be suitable for compiling on the target system.



Now build the compiler:


```wiki
<H>$ make
```


**NOTE**: ./libraries/unix/dist-install/build/System/Posix/\*.hs gets
filled with offsets, sizes, etc. for the host, not the target. This
needs to be fixed by hand after make (marked by "LINE"), and then run
make again to generate \*.hc**
**



You may need to work around problems that occur due to differences
between the host and target platforms. You may also need to use `make -k`
in order to ignore unimportant build failures in the RTS.


```wiki
<H>$ rm -f list mkfiles boot.tar.gz
<H>$ find . -name "*.hi" >> list
<H>$ find . -name "*.hc" >> list
<H>$ find . -name "*_stub.c" >> list
<H>$ find . -name "*_stub.h" >> list
<H>$ find . -name package-data.mk >> list
<H>$ find . -name package.conf.d >> list
<H>$ find . -name package.conf.inplace >> list
<H>$ echo compiler/main/Config.hs >> list
<H>$ echo compiler/prelude/primops.txt >> list
<H>$ ls compiler/primop-*.hs-incl >> list
<H>$ find . -name .depend | sed -e 's/^/mkdir -p `dirname /' -e 's/$/`/' >> mkfiles
<H>$ find . -name .depend | sed "s/^/touch /" >> mkfiles
<H>$ echo mkfiles >> list
<H>$ tar -zcf boot.tar.gz -T list
```


**On the target machine**



Unpack `<H>/boot.tar.gz` to `<T>/`.


```wiki
<T>$ tar --touch -zxf boot.tar.gz
<T>$ sh mkfiles
```


Put this in `<T>/mk/build.mk`:


```wiki
OMIT_PHASE_0 = YES
OMIT_PHASE_1 = YES
GHC = false
GHC_STAGE1 =
GHC_PKG_INPLACE =
GHC_CABAL_INPLACE =
DUMMY_GHC_INPLACE =
UNLIT =
NO_INCLUDE_DEPS = YES
GhcUnregisterised = YES
GhcLibWays = v
GhcRTSWays =
SplitObjs = NO
GhcWithNativeCodeGen = NO
GhcWithInterpreter = NO
GhcWithSMP = NO
ghc_stage2_v_EXTRA_CC_OPTS += -Llibraries/integer-gmp/gmp -lgmp -lm -lutil -lrt
utils/ghc-pkg_dist-install_v_EXTRA_CC_OPTS += -Llibraries/integer-gmp/gmp -lgmp -lm -lutil -lrt
```


You should also consider putting:


```wiki
SRC_CC_OPTS += -g -O0
```


in `<T>/mk/build.mk`. It'll make the resulting compiler slower, but it'll be a lot easier
if you get segfaults etc later on.


```wiki
<T>$ for c in libraries/*/configure; do ( cd `dirname $c`; sh configure ); done
```


Note that if you need some special arguments to configure on you platform (like --with-iconv-includes and --with-iconv-libraries on OpenBSD), you will have to pass them to the configure runs above, too.
You may also need a set of flags and/or libraries different from -lutil -lrt.


```wiki
<T>$ sed -i.bak "s#<H>#<T>#g" inplace/lib/package.conf.d/*.conf */*/package-data.mk */*/*/package-data.mk */*/*/*/package-data.mk
<T>$ touch -r inplace/lib/package.conf.d */*/package-data.mk */*/*/package-data.mk */*/*/*/package-data.mk compiler/stage*/build/Config.hs
```


Now make bootstrapping files; what we're really doing here is making
libffi, and libgmp if necessary:


```wiki
<T>$ make bootstrapping-files
```

```wiki
<T>$ make all_ghc_stage2      2>&1 | tee c.log
<T>$ make inplace/bin/ghc-pkg 2>&1 | tee gp.log
<T>$ make inplace/lib/unlit
```


NOTE: building inplace/bin/ghc-pkg currently fails. This has to be fixed.



Don't bother with running `make install` in the newly bootstrapped
tree; just use the compiler in that tree to build a fresh compiler
from scratch, this time without booting from C files.  Before doing
this, you might want to check that the bootstrapped compiler is
generating working binaries:


```wiki
$ cat >hello.hs
main = putStrLn "Hello World!\n"
^D
$ <T>/inplace/bin/ghc-stage2 hello.hs -o hello
$ ./hello
Hello World!
```


Once you have the unregisterised compiler up and running, you can use
it to start a registerised port.  The following sections describe the
various parts of the system that will need architecture-specific
tweaks in order to get a registerised build going.


## Porting the RTS



The following files need architecture-specific code for a registerised
build:


<table><tr><th>`includes/MachRegs.h`</th>
<td>
Defines the STG-register to machine-register
mapping.  You need to know your platform's C calling
convention, and which registers are generally available
for mapping to global register variables.  There are
plenty of useful comments in this file.
</td></tr></table>


<table><tr><th>`includes/TailCalls.h`</th>
<td>
Macros that make proper tail-calls work.
</td></tr></table>


<table><tr><th>`rts/Adjustor.c`</th>
<td>
Support for `foreign import "wrapper"`.
Not essential for getting GHC bootstrapped, so this file
can be deferred until later if necessary.
</td></tr></table>


<table><tr><th>`rts/StgCRun.c`</th>
<td>
The little assembly layer between the C world and
the Haskell world.  See the comments and code for the
other architectures in this file for pointers.
</td></tr></table>


<table><tr><th>`rts/sm/MBlock.h`, `rts/sm/MBlock.c`</th>
<td>
These files are really OS-specific rather than
architecture-specific.  In `MBlock.h`
is specified the absolute location at which the RTS
should try to allocate memory on your platform (try to
find an area which doesn't conflict with code or dynamic
libraries).  In `Mblock.c` you might
need to tweak the call to `mmap()` for
your OS.
</td></tr></table>


## The splitter



The splitter is another evil Perl script
([driver/split/ghc-split.lprl](/trac/ghc/browser/ghc/driver/split/ghc-split.lprl)). Object splitting is what happens
when the `-split-objs` option is passed to GHC: the object file is
split into many smaller objects. This feature is used when building
libraries, so that a program statically linked against the library
will pull in less of the library.



The splitter has some platform-specific stuff; take a look and tweak
it for your system.


## The native code generator



The native code generator isn't essential to getting a
registerised build going, but it's a desirable thing to have
because it can cut compilation times in half.  The native code
generator is described in detail in [Commentary/Compiler/Backends/NCG](commentary/compiler/backends/ncg).


## GHCi



To support GHCi, you need to port the dynamic linker
([rts/Linker.c](/trac/ghc/browser/ghc/rts/Linker.c)).  The linker currently supports the
ELF and PEi386 object file formats - if your platform uses one of
these then things will be significantly easier.  The majority of Unix
platforms use the ELF format these days.  Even so, there are some
machine-specific parts of the ELF linker: for example, the code for
resolving particular relocation types is machine-specific, so some
porting of this code to your architecture and/or OS will probably be
necessary.



If your system uses a different object file format, then
you have to write a linker -- good luck!


