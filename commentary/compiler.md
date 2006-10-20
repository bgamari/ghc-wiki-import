# GHC Commentary: The Compiler



The compiler itself is written entirely in Haskell, and lives in the many sub-directories of the [compiler](/trac/ghc/browser/ghc/compiler) directory.  Here is a block diagram of its top-level structure:



[](/trac/ghc/attachment/wiki/Commentary/Compiler/ghc-top.png)


## Contents


- [Compiler Module Dependencies](module-dependencies) (deals with the arcane mutual recursions among GHC's many data types)
- [Coding guidelines](commentary/coding-style)

>
> >
> >
> >
> >
>

- [Compiling one module: HscMain](commentary/compiler/hsc-main)
- Key data types (Simon PJ's diagram is attached at the bottom of this document):

  - [The source language: HsSyn](commentary/compiler/hs-syn-type) 
  - [RdrNames, Modules, and OccNames](commentary/compiler/rdr-name-type)
  - [Names](commentary/compiler/name-type)
  - [Entities](commentary/compiler/entity-types): variables, type constructors, data constructors, and classes.
  - Types: [Type and Kind](commentary/compiler/type-type), [equality types and coercions](commentary/compiler/fc)
  - [The core language](commentary/compiler/core-syn-type)
  - [The STG language](commentary/compiler/stg-syn-type)
  - [The Cmm language](commentary/compiler/cmm-type)
  - ModIface, ModDetails, ModGuts
- Passes:

  - [Renamer](commentary/compiler/renamer)
  - Typechecker
  - Desugarer
  - Core-\>core

    - [Strictness analysis](commentary/compiler/strictness-analysis)
  - Core-\>CorePrep
  - CorePrep-\>Stg?
  - [The code generator](commentary/compiler/code-gen): Stg-\>Cmm

>
> >
> > >
> > > >
> > > >
> > > >
> > > >
> > >
> >
>

- [The GHC API](commentary/compiler/api)
- [Symbol names and the Z-encoding](commentary/compiler/symbol-names)
- Template Haskell?
- [Wired-in and known-key things](commentary/compiler/wired-in)
- [Packages](commentary/compiler/packages)
- The Finder?
- [Backends](commentary/compiler/backends):

  - [C code generator](commentary/compiler/backends/ppr-c)
  - [Native code generator](commentary/compiler/backends/ncg)


The GHC API is the interface exported by compiler/main/GHC.hs. To compile a Haskell module that uses the GHC API, use the flag `-package ghc` (in GHC 6.6 and later). GHC itself contains a few front-ends: 


- The "one-shot" mode, where GHC compiles each file on the command line separately (eg. `ghc -c Foo.hs`). This mode is implemented
  directly on top of [HscMain](commentary/compiler/hsc-main), since it compiles only one file at a time. In fact, this is all that   
  GHC consisted of prior to version 5.00 when GHCi and --make were introduced.

- GHCi, the interactive environment, is implemented in [compiler/ghci/InteractiveUI.hs](/trac/ghc/browser/ghc/compiler/ghci/InteractiveUI.hs) and sits squarely on top of the GHC
  API.


 


- `--make` is almost a trivial client of the GHC API, and is implemented in [compiler/main/Main.hs](/trac/ghc/browser/ghc/compiler/main/Main.hs). 

- `-M`, the Makefile dependency generator, is also a client of the GHC API and is implemented in
  [compiler/main/DriverMkDepend.hs](/trac/ghc/browser/ghc/compiler/main/DriverMkDepend.hs). 


Note that since GHC is packaged as a single binary, all of these front-ends are present, and there is a single command-line interface implemented in [compiler/main/Main.hs](/trac/ghc/browser/ghc/compiler/main/Main.hs).


