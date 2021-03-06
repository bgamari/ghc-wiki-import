CONVERSION ERROR

Original source:

```trac
= Modifying the build system =

This section covers making changes to the GHC build system.  We'll give some general advice on how to work with the build system, and then describe a few common scenarios, such as how to add a new source file.

Note that before making any non-trivial changes to the build system you should acquaint yourself with the overall [wiki:Building/Architecture architecture].  Even if you're already familiar with GNU make, the GHC build system is probably quite different from most `Makefile`-based build systems you've encountered before.  

Incidentally, it's a good idea to have a copy of the 
[http://www.gnu.org/software/make/manual/make.html GNU make documentation] to hand when working with the build system.

== Debugging ==

When the build system doesn't do what you want, the results can be
pretty cryptic.  Often the problem is that something is being built in
the wrong order, or some variable isn't being propagated to the places
you thought it was.  How do you go about debugging the build system?

Here are the techniques that we use.  Note, for many of these diagnosis techniques you may want to invoke
'''make''' on `ghc.mk` directly using `make -f ghc.mk`, to bypass the
[wiki:Building/Architecture/Idiom/PhaseOrdering phase ordering] machinery of the top-level
`Makefile`.

 `$(warning ... message ...)`::
  equivalent to "printf-debugging" in a C program: this causes
  '''make''' to print a message when it reads the `$(warning ..)`
  expression, and the message can include variable references.  Very
  useful for finding out what '''make''' thinks the value of a
  variable is at a particular point in the `Makefile`, or for finding
  out the parameters for a particular macro call.    Want to test your hypothesis about what the variable `rts_C_SRCS` contains?  Just add a `$(warning $(rts_C_SRCS))` somewhere.

 `make show VALUE=VAR`::
  prints the value of variable `VAR`.  Useful for quick diagnosis.

 `make show! VALUE=VAR`::
  same as `make show`, but works right after ./configure (it skips reading package-data.mk files).

 `make TRACE=1`::
  prints messages about certain macros that are called and their arguments.  This is basically a system of `$(warning)` calls enabled when the value of `$(TRACE)` is non-empty.  To see how it works, look at the file [source:rules/trace.mk], and feel free to add trace calls to more places in the build system.

 `make --debug=b --debug=m`::
   causes '''make''' to show the sequence of dependencies that it is
   following, which will often tell you ''why'' something is being
   built.  This can help to track down missing or incorrect
   dependencies.

 `make -p`::
  prints out all the generated rules and variables.  The output can be
  huge; so pipe it to a file, and search through it for the bits of
  interest.

== Scenarios ==

The following are a collection of common changes you might want to make to the build system, with the exact steps required in each case.

=== Adding a source file to GHC ===

GHC is in two parts: 

  * the `ghc` package, which comprises most of the GHC source code, can be found in the `compiler` directory.  
  * the `ghc` program itself, consists of a single `Main` module that imports the `ghc` package.  It is found in the `ghc` directory.

Like any Cabal package, the `ghc` package has a `ghc.cabal` file, except that in this case the file is preprocessed by `configure` from the original: [source:compiler/ghc.cabal.in].  Be careful not to modify the preprocessed version, `ghc.cabal`, as it will be overwritten next time you run `configure`.

To add a module to the `ghc` package:

 * Add your module to the `exposed-modules` section of [source:compiler/ghc.cabal.in]
 * {{{cd compiler; make stage2}}}

=== Removing a source file from GHC ===

To retire a GHC source file that is no longer needed:

  * Remove the working copy of the file (git will notice it is gone), or use {{{git rm}}}.
  * Remove the module from the list of modules in [source:compiler/ghc.cabal.in].
  * To remove all mention of the file from derived dependency files, it is necessary to do something on the
    order of
    {{{
cd $TOP
sh config.status
make all_compiler_stage1
    }}}
    or {{{make all_compiler_stage2}}} if you prefer.

  If you wish something slower but more confident, you may {{{cd $TOP; ./configure; make}}}.

=== Adding a source file to the RTS ===

The RTS picks up source files automatically, using '''make''''s `$(wildcard ...)` function.  So to add files to the RTS, just put your source file in `rts`, and

{{{
$ cd rts; make
}}}

To have the change propagated to the stage 2 compiler, either go and make stage 2 explicitly, or issue a top-level `make`.

=== Adding a new package ===

Adding a new package is quite straightforward:

 * To arrange that the package is known to `./boot`, add an entry to the [source:packages] file.
 * run `perl boot` to generate the `ghc.mk` and `GNUmakefile` files in your package's build.
 * Add an entry to the `PACKAGES` variable in [source:ghc.mk].  The list in `PACKAGES` is kept in dependency order: each package must appear after the packages it depends on.

That's it: doing a top-level `make` should now build your package and bring everything else up to date.

=== Adding a program ===

Utility programs live in `utils`, for example `utils/hpc`.  Each program has a simple `ghc.mk` file, for example the one for `hpc` looks like this:

{{{
utils/hpc_dist_MODULES = Main HpcCombine HpcDraft HpcFlags HpcLexer HpcMap \
			 HpcMarkup HpcOverlay HpcParser HpcReport HpcSet \
			 HpcShowTix HpcUtils
utils/hpc_dist_HC_OPTS = -cpp -package hpc
utils/hpc_dist_INSTALL = YES
utils/hpc_dist_PROG    = hpc$(exeext)
$(eval $(call build-prog,utils/hpc,dist,1))
$(eval $(call bindist,utils/hpc,ghc.mk))
}}}

You also need to modify the top-level `ghc.mk` file and add a `BUILD_DIRS` entry for your new program:

{{{
BUILD_DIRS += utils/hpc
}}}

This is all that is necessary to build and install a program.  Taking each of these lines in turn:

{{{
utils/hpc_dist_MODULES = Main HpcCombine HpcDraft HpcFlags HpcLexer HpcMap \
			 HpcMarkup HpcOverlay HpcParser HpcReport HpcSet \
			 HpcShowTix HpcUtils
}}}

remember that variable names all begin with ''directory''_''distdir'' (see [wiki:Building/Architecture/Idiom/VariableNames Idiom: variable names]), and in this case the directory is `utils/hpc`, and the distdir is just `dist`.  The variable `utils/hpc_dist_MODULES` specifies the list of Haskell modules that make up the `hpc` program.

{{{
utils/hpc_dist_HC_OPTS = -cpp -package hpc
}}}

This defines any extra options that need to be passed to GHC when compiling `hpc`.

{{{
utils/hpc_dist_INSTALL = YES
}}}

This line indicates that we want the `hpc` program to be installed by `make install`.  The default is not to install programs.

{{{
utils/hpc_dist_PROG    = hpc$(exeext)
}}}

This specifies the name of the program.  The variable `$(exeext)` contains `.exe` on Windows, and is otherwise empty.

{{{
$(eval $(call build-prog,utils/hpc,dist,1))
}}}

This is a call to the `build-prog` macro, whose definition can be found in [source:rules/build-prog.mk].  This expands to the code for actually building the program, and it takes three arguments: the directory, the distdir, and the GHC stage to use for building (in this case we're using stage 1).  The `build-prog` macro expects to find certain variables defined, namely ''directory''_''distdir''`_PROG`, and ''directory''_''distdir''`_MODULES`.

Finally, for programs that we want to install, we need to include the `ghc.mk` file in a binary distribution:

{{{
$(eval $(call bindist,utils/hpc,ghc.mk))
}}}

```
