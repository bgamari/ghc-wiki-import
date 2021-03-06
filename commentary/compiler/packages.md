


# GHC Commentary: Packages



This section documents how GHC implements packages.  You should also look at


- [The Packages section of the Users Guide](http://www.haskell.org/ghc/docs/latest/html/users_guide/packages.html)
- [The Cabal documentation](http://www.haskell.org/ghc/docs/latest/html/Cabal/index.html)
- Various `Distribution.*` modules in the Cabal package, eg.   
  [Distribution.Package](http://www.haskell.org/ghc/docs/latest/html/libraries/Cabal/Distribution-Package.html).
- [ModIface, ModDetails, ModGuts](commentary/compiler/module-types): Details of some of the types involve in ghc's handling of modules and packages.
- [Packages](commentary/packages): A commentary on packages at a slightly higher level, user orientated view.
- [Package Reorganisation](commentary/packages/package-reorg): Some rough notes (old) on reorganising the package systems and those included by default with GHC.
- [Package Proposal](commentary/packages/ghc-packages-proposal): More rough notes (old) on changing the package system in GHC.

## Overview



A package consists of  zero or more Haskell modules, compiled to object code and placed in a single library file `libHS<pkg>.a`. We also provide a version of the `.a` file linked together as a single object file, usually named `HS<pkg>.o`. This is due to the fact that GHCi's [linker](commentary/rts/interpreter#linker) used to not be able to load `.a` files (this is no longer true with `loadArchive`). A package will also contain a dynamic library file `libHS<pkg>-ghc<ghc-ver>.so`.


## Package databases



GHC draws its information about what packages are installed from one or more package databases.  A package database is a file containing a value of type \[ [InstalledPackageInfo](http://www.haskell.org/ghc/docs/latest/html/libraries/Cabal/Distribution-InstalledPackageInfo.html#t%3AInstalledPackageInfo) \], rendered as text via `show`.  Also, GHC allows the system package databases to be in the form of a directory of files, each of which contains a `[InstalledPackageInfo]` (in the future this may be extended to allow all packages databases to have this form).  Note: the exact representation of a package database is intended to be private to GHC, which is why we provide the `ghc-pkg` tool to manipulate it.


## Package-related types



Source files: [compiler/main/PackageConfig.hs](/trac/ghc/browser/ghc/compiler/main/PackageConfig.hs), [compiler/main/Packages.lhs](/trac/ghc/browser/ghc/compiler/main/Packages.lhs)


<table><tr><th>`PackageId`</th>
<td>
The most important package type inside ghc is `PackageId`, representing the full name of a package (including its version).
It is represented as a `FastString` for fast comparison.
</td></tr></table>


<table><tr><th>`PackageConfig`</th>
<td>
The information contained in the package database about a package.  Currently this is a synonym for `InstalledPackageInfo`,
later it might contain extra GHC-specific info, or have a more optimised representation.
</td></tr></table>


<table><tr><th>`PackageConfigMap`</th>
<td>
A mapping (actually `UniqFM`) from `PackageId` to `PackageConfig`.
</td></tr></table>


<table><tr><th>`PackageState`</th>
<td>
Everything the compiler knows about the package database.  This is built by `initPackages` in 
[compiler/main/Packages.lhs](/trac/ghc/browser/ghc/compiler/main/Packages.lhs), and stashed in the `DynFlags`.
</td></tr></table>


## Modules



GHC (from version 6.6) allows a single program to contain multiple modules with the same name, as long as the duplicates all come from different packages.  In other words, the pair (package name, module name) must be unique within a program.  GHC implements this with the [Module type](commentary/compiler/rdr-name-type#the-module-and-modulename-types), which contains a `PackageId` and the `ModuleName` of a module.  For any `Module`, we can therefore ask which package it comes from.



This means that the `Module` type is not `Uniqable`, so we can't use `Module` as the key in a `UniqFM`, which is sad.  We explored various schemes for extracting uniques from `Modules`, but didn't find anything attractive enough.  Another problem with the current scheme is that everytime we refer to a `Module` in an interface file, it gives rise to two words in the binary representation.  Our current plan is to improve the binary representation in `.hi` files to mitigate this, but this is currently one reason why in GHC 6.6 interface files are larger than in 6.4.



Source code: [compiler/basicTypes/Module.lhs](/trac/ghc/browser/ghc/compiler/basicTypes/Module.lhs).


## Finding modules



When we import a module, how does GHC decide where the module lives (e.g. to read in the [interface files](commentary/compiler/iface-files) for type checking)?  This is done in two parts.



First, we generate a module map in [compiler/main/Packages.lhs](/trac/ghc/browser/ghc/compiler/main/Packages.lhs) in \@mkModuleMap@, which is stored in the \@PackageState@. This module map is based off of the package databases (by default the global and local databases), with in-memory modifications to the hidden/trusted flags associated with modules.  This map says, for any given module name, what packages define it. (For the purpose of error reporting, we collect up non-exposed modules, since a user may mistakenly try to import them.)



Next, when we actually need to lookup a module in [compiler/main/Finder.lhs](/trac/ghc/browser/ghc/compiler/main/Finder.lhs) using \@findExposedPackageModule@. Now, we lookup the module name in our map, filter out entries which are hidden (either because their packages were hidden or they were not exposed), and if there is exactly one result, we succeed. If we don't find anything, a fuzzy lookup finds similar module names; if we find multiple results we report them appropriately.


## The current package



There is a notion of which package we are compiling, set by the `-package-name` flag on the command line.  In the absence of a `-package-name` flag, the default package `main` is assumed.



To find out what the current package is, grab the field `thisPackage` from `DynFlags` (see [compiler/main/DynFlags.hs](/trac/ghc/browser/ghc/compiler/main/DynFlags.hs)).


## Special packages



Certain packages are special, in the sense that GHC knows about their existence and something about their contents.  Any [Name](commentary/compiler/name-type) that is [wired-in](commentary/compiler/wired-in) (see [compiler/prelude/PrelNames.lhs](/trac/ghc/browser/ghc/compiler/prelude/PrelNames.lhs)) must by definition contain a `Module`, and that module must therefore contain a `PackageId`.  But the `PackageId` includes the version, so does this mean we have to somehow find out the version of the `base` package (for example) and bake it into the GHC binary?



We took the view that it should be possible to upgrade these packages independently of GHC, so long as you don't change any of the information that GHC knows about the package (eg. the type of `fromIntegral` or what module things come from).  Therefore we shouldn't bake in the version of any packages.  So the `PackageId` for the `base` package inside GHC is simply `base`: we explicitly strip off the version number for special packages wherever they occur.  



This does have the consequence that you cannot use multiple versions of a special package simultaneously in a program, but we believe that is unlikely for these packages anyway.  Another consequence is that symbol names for entities from special packages will not include the version number, which saves some space in the object files.



The following packages are special in GHC 7.8:


- `ghc-prim`
- `integer-gmp`
- `base`
- `rts`
- `template-haskell`
- `dph-seq` and `dph-par`


The definition of which packages are special and the stripping of versions from the package database happens in `findWiredInPackages` in [compiler/main/Packages.lhs](/trac/ghc/browser/ghc/compiler/main/Packages.lhs): we literally just rewrite the `sourcePackageId` associated with the package.


## Symbol names



All symbol names in the object code have the package name prepended (plus an underscore) so that modules of the same name from different packages do not clash.  We assume the symbol namespace is global, which is the worst case - allegedly there are ways to have semi-private namespaces on some platforms but we haven't explored that.



There is one exception: we don't prepend `main_` to symbols from the main package, because there can only ever be one main package.  This is a small optimisation.



Source code: see the `Outputable` instance for `Module` in [compiler/basicTypes/Module.lhs](/trac/ghc/browser/ghc/compiler/basicTypes/Module.lhs).


## Dynamic linking



Packages have another purpose when it comes to dynamic linking: each package is a single dynamically-linked library.  This is an important property on systems where making intra-library calls is different from inter-library calls (eg. Windows DLLs).  Even on systems where we only need to generate a single kind of call, making a data reference within a single library is cheaper than a data reference in another library, so knowing which is which is important.



At the time of writing (GHC 6.6) GHC doesn't have working support for generating multi-DLL Haskell programs, but it worked in the past and work is underway to resurrect it.  Dynamic libraries currently only work on MacOS X/PowerPC.


## Reexported modules



Starting with GHC 7.10 ([\#8407](https://gitlab.staging.haskell.org/ghc/ghc/issues/8407)), we will have support for reexporting modules from other packages you depend upon.  How does this work?  In the package database, a package not only declare that it exposes a module, but it can also say that it reexports a module, with an indirection to the defining package (and original module name, if it is renamed).  There is a twist however: this simple strategy could require GHC to follow a chain of indirections in the package database.  Instead, we maintain the invariant that an indirection always points to the original defining module. To enforce this invariant, `ghc-pkg` processes installed package info with `resolveReexports`, shortcutting the indirections before adding a package to the database.


## Packages in a GHC build



When GHC is building, it constructs two package databases:


- `driver/package.conf`: the package database that will be installed if you say `make install`.  To inspect or
  modify this database, use `utils/ghc-pkg/ghc-pkg-inplace -f <somewhere>/driver/package.conf`.

- `driver/package.conf.inplace`: the same, but paths points to the build tree so that GHC can be run without installing.
  To inspect or modify this database,  use `utils/ghc-pkg/ghc-pkg-inplace`.


Both of these databases start empty: `make boot` in `driver` creates an empty database in each file.  Then, packages are registered into each database when `make boot` runs in a package directory.



NOTE: packages must be registered in dependency order.  The build system arranges this normally, but if you build parts of the tree by hand you might violate this rule.  If a package is registered before its dependencies, you might not get an error message, but something will go wrong later (probably a missing package dependency).  The reason is, to make it easier to register packages, we don't specify full version numbers in the `depends` field of a package configuration, leaving `ghc-pkg` to fill it in from the database, but if the dependency isn't present in the database, `ghc-pkg` silently registers it anyway (because we use `--force`... that's another story).


### Refreshing your package databases



Sometimes things can get out of sync in your build tree, if a package version was bumped for example.  If you get into trouble, just `make clean` in your tree.


