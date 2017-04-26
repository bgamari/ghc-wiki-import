# Backpack



This is the launch page for Backpack, actively maintained by Edward (as of Apr 2017). 



[
Backpack](http://plv.mpi-sws.org/backpack/) is a proposal for retrofitting Haskell with an applicative, mix-in module system. It has been implemented in GHC 8.2.



The documentation for how to use Backpack is a bit scattered about at this point, but here are useful, up-to-date (as of 2017-04-02, prior to GHC 8.2's release) references:


- This pair of blog posts: Try Backpack, [
  ghc --backpack](http://blog.ezyang.com/2016/10/try-backpack-ghc-backpack/) and [
  Cabal packages](http://blog.ezyang.com/2017/01/try-backpack-cabal-packages/) have up-to-date tutorials for using the main features of Backpack, with and without Cabal.

- The [
  GHC manual section on module signatures](https://downloads.haskell.org/~ghc/master/users-guide/separate_compilation.html#module-signatures) gives the gory details about how Backpack's signature files (hsig) work. A more user-friendly version of this can be found on [
  Haskell wiki "Module signature"](https://wiki.haskell.org/Module_signature)

- There is not yet a manual entry in Cabal for how Cabal works. This section is under development.

- Edward Z. Yang's [
  thesis](https://github.com/ezyang/thesis/releases) contains detailed information about the specification and implementation of Backpack.

- Hackage does not yet support uploads of Backpack-using packages. [
  next.hackage](http://next.hackage.haskell.org:8080/) is a Hackage instances running a development branch of Hackage that can handle Backpack; for now, Backpack-enabled packages should be uploaded here.


Some more out-of-date documents:


- [
  Backpack specification](https://github.com/ezyang/ghc-proposals/blob/backpack/proposals/0000-backpack.rst). This was subsumed by my thesis but once Backpack stabilizes it will be worth distilling the thesis PDF back into a more web-friendly format.

## Known gotchas



**Make sure cabal-version is recent enough.** ([
\#4448](https://github.com/haskell/cabal/issues/4448)) If you set the `cabal-version` of your package too low, you may get this error:


```wiki
Error:
    Mix-in refers to non-existent package 'pkg'
    (did you forget to add the package to build-depends?)
    In the stanza 'executable myexe'
    In the inplace package 'pkg'
```


This is because internal libraries are feature-gated by the `cabal-version` of your package. Setting it to `cabal-version: >= 2.0` is enough to resolve the problem.



**You can't instantiate a dependency with a locally defined module.** Consider the following package:


```wiki
library
  other-modules: StrImpl
  build-depends: foo-indef
  mixins: foo-indef requires (Str as StrImpl)
```


This looks like it should work, but actually it will fail:


```wiki
Error:
    Non-library component has unfilled requirements: StrImpl
    In the stanza 'library'
    In the inplace package 'mypkg-1.2'
```


The reason for this is Backpack does not (currently) support instantiating a package with a locally defined module: since the module can turn around and \*import\* the mixed in `foo-indef`, which would result in mutual recursion (not presently supported.)



To solve this problem, just create a new library to define the implementing module. This library can be in the same package using the convenience library mechanism:


```wiki
library str-impl
  exposed-modules: StrImpl

library
  build-depends: str-impl, foo-indef
  mixins: foo-indef requires (Str as StrImpl)
```

## Backpack-related tickets



Backpack-related tickets are marked with keyword 'backpack'. If the ticket is assigned to ezyang, it means he's planning on working on it.




  
  
  
  
  
    
  
  

<table><tr><td>
      </td>
<th>
        
        Ticket (Ticket query: keywords: backpack, status: new, max: 0, col: id,
col: type, col: summary, col: priority, col: owner, order: id)
      </th>
<th>
        
        Type (Ticket query: keywords: backpack, status: new, max: 0, col: id,
col: type, col: summary, col: priority, col: owner, order: type)
      </th>
<th>
        
        Summary (Ticket query: keywords: backpack, status: new, max: 0, col: id,
col: type, col: summary, col: priority, col: owner, order: summary)
      </th>
<th>
        
        Priority (Ticket query: keywords: backpack, status: new, max: 0,
col: id, col: type, col: summary, col: priority, col: owner, desc: 1,
order: priority)
      </th>
<th>
        
        Owner (Ticket query: keywords: backpack, status: new, max: 0, col: id,
col: type, col: summary, col: priority, col: owner, order: owner)
      </th>
<td>
    </td>
<td></td>
<td></td>
<td></td>
<td></td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#1409](https://gitlab.staging.haskell.org/ghc/ghc/issues/1409)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      feature request
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Allow recursively dependent modules transparently (without .hs-boot or anything)](https://gitlab.staging.haskell.org/ghc/ghc/issues/1409)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#9351](https://gitlab.staging.haskell.org/ghc/ghc/issues/9351)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      feature request
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [add ability to version symbols .c for packages with C code](https://gitlab.staging.haskell.org/ghc/ghc/issues/9351)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#10749](https://gitlab.staging.haskell.org/ghc/ghc/issues/10749)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Boot file instances should imply superclasses](https://gitlab.staging.haskell.org/ghc/ghc/issues/10749)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      ezyang
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#10827](https://gitlab.staging.haskell.org/ghc/ghc/issues/10827)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      feature request
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [GHCi should support interpeting multiple packages/units with separate DynFlags](https://gitlab.staging.haskell.org/ghc/ghc/issues/10827)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#12703](https://gitlab.staging.haskell.org/ghc/ghc/issues/12703)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      feature request
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Expand Backpack's signature matching relation beyond definitional equality](https://gitlab.staging.haskell.org/ghc/ghc/issues/12703)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#13151](https://gitlab.staging.haskell.org/ghc/ghc/issues/13151)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      task
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Make all never-exported IfaceDecls implicit](https://gitlab.staging.haskell.org/ghc/ghc/issues/13151)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      ezyang
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#13266](https://gitlab.staging.haskell.org/ghc/ghc/issues/13266)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Source locations from signature merging/matching are bad](https://gitlab.staging.haskell.org/ghc/ghc/issues/13266)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#13469](https://gitlab.staging.haskell.org/ghc/ghc/issues/13469)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      feature request
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [-fdefer-type-errors for Backpack](https://gitlab.staging.haskell.org/ghc/ghc/issues/13469)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#14212](https://gitlab.staging.haskell.org/ghc/ghc/issues/14212)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Give better error message with non-supported Backpack/TH use](https://gitlab.staging.haskell.org/ghc/ghc/issues/14212)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#14478](https://gitlab.staging.haskell.org/ghc/ghc/issues/14478)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      feature request
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Abstract pattern synonyms (for hsig and hs-boot)](https://gitlab.staging.haskell.org/ghc/ghc/issues/14478)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#15391](https://gitlab.staging.haskell.org/ghc/ghc/issues/15391)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Maybe ghc-pkg register should unregister packages with "incompatible" signatures](https://gitlab.staging.haskell.org/ghc/ghc/issues/15391)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#15984](https://gitlab.staging.haskell.org/ghc/ghc/issues/15984)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Backpack accepts ill-kinded instantiations. Can cause GHC panic](https://gitlab.staging.haskell.org/ghc/ghc/issues/15984)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#10266](https://gitlab.staging.haskell.org/ghc/ghc/issues/10266)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      task
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Split base for Backpack](https://gitlab.staging.haskell.org/ghc/ghc/issues/10266)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      low
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      ezyang
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#10681](https://gitlab.staging.haskell.org/ghc/ghc/issues/10681)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      feature request
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Teach GHC to interpret all hs files as two levels of hs-boot files (abstract types only/full types + values)](https://gitlab.staging.haskell.org/ghc/ghc/issues/10681)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      low
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      ezyang
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#12680](https://gitlab.staging.haskell.org/ghc/ghc/issues/12680)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      feature request
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Permit type equality instances in signatures](https://gitlab.staging.haskell.org/ghc/ghc/issues/12680)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      low
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#13149](https://gitlab.staging.haskell.org/ghc/ghc/issues/13149)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      task
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Giving Backpack a Promotion](https://gitlab.staging.haskell.org/ghc/ghc/issues/13149)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      low
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#13262](https://gitlab.staging.haskell.org/ghc/ghc/issues/13262)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      feature request
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Allow type synonym family application in instance head if it has no free variables](https://gitlab.staging.haskell.org/ghc/ghc/issues/13262)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      low
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#13361](https://gitlab.staging.haskell.org/ghc/ghc/issues/13361)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Better type synonym merging/subtyping for Backpack](https://gitlab.staging.haskell.org/ghc/ghc/issues/13361)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      low
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#13765](https://gitlab.staging.haskell.org/ghc/ghc/issues/13765)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [GHC cannot parse valid Haskell98 whose first identifier is named signature](https://gitlab.staging.haskell.org/ghc/ghc/issues/13765)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      low
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#14210](https://gitlab.staging.haskell.org/ghc/ghc/issues/14210)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      feature request
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [bkp files cannot find TemplateHaskell symbols (even without Backpack features)](https://gitlab.staging.haskell.org/ghc/ghc/issues/14210)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      low
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#10871](https://gitlab.staging.haskell.org/ghc/ghc/issues/10871)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      feature request
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Implement "fat" interface files which can be directly compiled without source](https://gitlab.staging.haskell.org/ghc/ghc/issues/10871)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      lowest
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      ezyang
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#12717](https://gitlab.staging.haskell.org/ghc/ghc/issues/12717)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      feature request
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Permit data types in signatures to be implemented with equivalent pattern synonyms (and vice versa)](https://gitlab.staging.haskell.org/ghc/ghc/issues/12717)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      lowest
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr></table>


  



