
[
Backpack](http://plv.mpi-sws.org/backpack/) is a proposal for retrofitting Haskell with an applicative, mix-in module system. The theory of Backpack is developed in the paper and its accompanying technical appendix. The purpose of this page is to track implementation progress. (Wondering what happened to the old text? Check the history; it will be integrated into the implementation design doc eventually).



See also [CabalDependency](cabal-dependency)


## Reading


- [
  The Backpack documentation directory](https://git.haskell.org/ghc.git/blob/HEAD:/docs/backpack/). You'll have to build them (just `make`), but here are the files of interest:

  - algorithm.tex specifies the abstract core Backpack algorithms for GHC.
  - backpack-manual.tex is a user-facing manual for using Backpack.
  - backpack-impl.tex is an old but interesting technical document describing many of the technical tradeoffs in our design.
- [
  What's a module system good for anyway?](http://blog.ezyang.com/2014/08/whats-a-module-system-good-for-anyway/) Motivates \*why\* you might care about Backpack
- The [commentary pages about packages](commentary/compiler/packages)

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


  



