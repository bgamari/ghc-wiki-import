
This page documents some cleanups that I (Sylvain Henry) would like to perform on GHC's code base.


## Step 1: introduce basic module hierarchy



Implement the [proposal for hierarchical module structure in GHC](module-dependencies/hierarchical) ([\#13009](https://gitlab.staging.haskell.org/ghc/ghc/issues/13009)).



It consists in renaming/moving modules.



Issues:


- some modules in `base` already use useful names (e.g., GHC.Desugar) to export a few builtin utility functions

  - for now, I'll try to avoid conflicts (e.g., what would be GHC.Desugar in GHC is GHC.Desugar.Main)
  - maybe we should put all GHC extensions in base under GHC.Exts.\*

## Step 2: clearly separate GHC-the-program and GHC's API


- Make the GHC API purer

### Abstract file loading (i.e. pluggable Finder)



Currently the Finder assumes that a filesystem exists into which it can find some packages/modules.



I would like to add support for module sources that are only available in memory or that can be retrieved from elsewhere (network, etc.).



Something similar to Java's class loaders.


### Abstract error reporting and logging (i.e. pluggable Logger)



Allow new frontends (using GHC API) to use HTML reporting, etc.


- Avoid dumping to the filesystem and/or stdout/stderr
- Use data types instead of raw SDoc reports

### Step 3: clearly separate phases


- split DynFlags to only pass the required info to each pass

  - e.g. only the required hooks
- use data types to report phase statistics, intermediate representations, etc.