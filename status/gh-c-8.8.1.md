# GHC plans for 8.8.1






This page is our road-map for what will be in 8.8.



If you believe your favorite thing belongs in this list, but isn't there, please yell.  If it's not in the road map, it probably won't get done.  Without a lot of support, many things in the road map won't get done either, so we need your help!


## Dates


- 18 November 2018: Cut release branch
- 25 November 2018: Release alpha1
- 16 December 2018: Release alpha2
- 6 January 2019: Release alpha3
- 27 January 2019: Release alpha4
- 17 February 2019: Release beta1
- 15 March 2019: Final release

## Libraries Status



See Libraries? and [Migration/8.8](migration/8.8).


## Release highlights (planned)



Below are the major highlights of 8.8.


### Compiler


- A safer and more efficient `with#` combinator to control object lifetime ([\#14375](https://gitlab.staging.haskell.org/ghc/ghc/issues/14375))
- Improved compilation time for type-family-heavy programs ([\#8095](https://gitlab.staging.haskell.org/ghc/ghc/issues/8095), [
  Phab:D4766](https://phabricator.haskell.org/D4766))
- More efficient code generation for nested closures ([\#14461](https://gitlab.staging.haskell.org/ghc/ghc/issues/14461))
- Next iteration of [Trees That Grow](implementing-trees-that-grow) (tickets/patches for this?)
- Continued focus on performance:

  - Some possible tickets: [\#15418](https://gitlab.staging.haskell.org/ghc/ghc/issues/15418), [\#15455](https://gitlab.staging.haskell.org/ghc/ghc/issues/15455), [\#14980](https://gitlab.staging.haskell.org/ghc/ghc/issues/14980), [\#14013](https://gitlab.staging.haskell.org/ghc/ghc/issues/14013), [\#15488](https://gitlab.staging.haskell.org/ghc/ghc/issues/15488), [\#15519](https://gitlab.staging.haskell.org/ghc/ghc/issues/15519), [\#14062](https://gitlab.staging.haskell.org/ghc/ghc/issues/14062), [\#14035](https://gitlab.staging.haskell.org/ghc/ghc/issues/14035), [\#15176](https://gitlab.staging.haskell.org/ghc/ghc/issues/15176), [\#15304](https://gitlab.staging.haskell.org/ghc/ghc/issues/15304)
  - New codelayout algorithm for the NCG: [\#15124](https://gitlab.staging.haskell.org/ghc/ghc/issues/15124)
  - Optimize based on limited static analysis: [\#14672](https://gitlab.staging.haskell.org/ghc/ghc/issues/14672)
- A late lambda lifting optimisation on STG ([\#9476](https://gitlab.staging.haskell.org/ghc/ghc/issues/9476))
- More locations where users can write `forall`: [
  https://github.com/ghc-proposals/ghc-proposals/blob/master/proposals/0007-instance-foralls.rst](https://github.com/ghc-proposals/ghc-proposals/blob/master/proposals/0007-instance-foralls.rst)

### Build system and miscellaneous changes


- [
  Reinstallable lib:ghc](https://mail.haskell.org/pipermail/ghc-devs/2017-July/014424.html)
- The Hadrian build system will hopefully become the default

## Landed in `master` branch


### Library changes


### Build system and miscellaneous changes


## Tickets marked merge with no milestone




  
  
  
  
  
    

## Status: merge (1 match)


  
  

<table><tr><td>
      </td>
<th>
        
        Ticket (Ticket query: status: merge, milestone: , group: status, max: 0,
col: id, col: type, col: summary, col: priority, col: owner, desc: 1, order: id)
      </th>
<th>
        
        Type (Ticket query: status: merge, milestone: , group: status, max: 0,
col: id, col: type, col: summary, col: priority, col: owner, order: type)
      </th>
<th>
        
        Summary (Ticket query: status: merge, milestone: , group: status,
max: 0, col: id, col: type, col: summary, col: priority, col: owner,
order: summary)
      </th>
<th>
        
        Priority (Ticket query: status: merge, milestone: , group: status,
max: 0, col: id, col: type, col: summary, col: priority, col: owner,
order: priority)
      </th>
<th>
        
        Owner (Ticket query: status: merge, milestone: , group: status, max: 0,
col: id, col: type, col: summary, col: priority, col: owner, order: owner)
      </th>
<td>
    </td>
<td></td>
<td></td>
<td></td>
<td></td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#16094](https://gitlab.staging.haskell.org/ghc/ghc/issues/16094)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [panic! (the 'impossible' happened): for powerpc-unknown-linux getRegister(ppc): I64\[I32\[BaseReg + 812\] + 64\]](https://gitlab.staging.haskell.org/ghc/ghc/issues/16094)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      trommler
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr></table>


  



## Tickets slated for 8.8.1


### merge/patch/upstream




  
  
  
  
  
    

## Status: merge (3 matches)


  
  

<table><tr><td>
      </td>
<th>
        
        Ticket (Ticket query: status: merge, status: patch, status: upstream,
milestone: 8.8.1, group: status, max: 0, col: id, col: type, col: summary,
col: priority, col: differential, col: owner, order: id)
      </th>
<th>
        
        Type (Ticket query: status: merge, status: patch, status: upstream,
milestone: 8.8.1, group: status, max: 0, col: id, col: type, col: summary,
col: priority, col: differential, col: owner, order: type)
      </th>
<th>
        
        Summary (Ticket query: status: merge, status: patch, status: upstream,
milestone: 8.8.1, group: status, max: 0, col: id, col: type, col: summary,
col: priority, col: differential, col: owner, order: summary)
      </th>
<th>
        
        Priority (Ticket query: status: merge, status: patch, status: upstream,
milestone: 8.8.1, group: status, max: 0, col: id, col: type, col: summary,
col: priority, col: differential, col: owner, desc: 1, order: priority)
      </th>
<th>
        
        Differential Rev(s) (Ticket query: status: merge, status: patch,
status: upstream, milestone: 8.8.1, group: status, max: 0, col: id, col: type,
col: summary, col: priority, col: differential, col: owner, order: differential)
      </th>
<th>
        
        Owner (Ticket query: status: merge, status: patch, status: upstream,
milestone: 8.8.1, group: status, max: 0, col: id, col: type, col: summary,
col: priority, col: differential, col: owner, order: owner)
      </th>
<td>
    </td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#15733](https://gitlab.staging.haskell.org/ghc/ghc/issues/15733)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      task
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Several links in GHC.Exts.Heap documentation are broken](https://gitlab.staging.haskell.org/ghc/ghc/issues/15733)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      D5257
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#15897](https://gitlab.staging.haskell.org/ghc/ghc/issues/15897)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Negative MUT time in +RTS -s -RTS when heap profiling is enabled](https://gitlab.staging.haskell.org/ghc/ghc/issues/15897)
                      
                      
                      
                      
                      
                      
                      
                      
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
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#16183](https://gitlab.staging.haskell.org/ghc/ghc/issues/16183)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [GHC HEAD regression: -ddump-splices incorrectly parenthesizes HsKindSig applications](https://gitlab.staging.haskell.org/ghc/ghc/issues/16183)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      [
https://gitlab.haskell.org/ghc/ghc/merge\_requests/121](https://gitlab.haskell.org/ghc/ghc/merge_requests/121)
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
          </td>
<th>
            
    

## Status: patch (12 matches)


  
          </th>
<td>
        </td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td></tr>
<tr><td>
      </td>
<th>
        
        Ticket (Ticket query: status: merge, status: patch, status: upstream,
milestone: 8.8.1, group: status, max: 0, col: id, col: type, col: summary,
col: priority, col: differential, col: owner, order: id)
      </th>
<th>
        
        Type (Ticket query: status: merge, status: patch, status: upstream,
milestone: 8.8.1, group: status, max: 0, col: id, col: type, col: summary,
col: priority, col: differential, col: owner, order: type)
      </th>
<th>
        
        Summary (Ticket query: status: merge, status: patch, status: upstream,
milestone: 8.8.1, group: status, max: 0, col: id, col: type, col: summary,
col: priority, col: differential, col: owner, order: summary)
      </th>
<th>
        
        Priority (Ticket query: status: merge, status: patch, status: upstream,
milestone: 8.8.1, group: status, max: 0, col: id, col: type, col: summary,
col: priority, col: differential, col: owner, desc: 1, order: priority)
      </th>
<th>
        
        Differential Rev(s) (Ticket query: status: merge, status: patch,
status: upstream, milestone: 8.8.1, group: status, max: 0, col: id, col: type,
col: summary, col: priority, col: differential, col: owner, order: differential)
      </th>
<th>
        
        Owner (Ticket query: status: merge, status: patch, status: upstream,
milestone: 8.8.1, group: status, max: 0, col: id, col: type, col: summary,
col: priority, col: differential, col: owner, order: owner)
      </th>
<td>
    </td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#16022](https://gitlab.staging.haskell.org/ghc/ghc/issues/16022)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Hadrian appears to link against libffi unconditionally](https://gitlab.staging.haskell.org/ghc/ghc/issues/16022)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      highest
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      [ Phab:D5427](https://phabricator.haskell.org/D5427)
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#16188](https://gitlab.staging.haskell.org/ghc/ghc/issues/16188)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [GHC HEAD-only panic (buildKindCoercion)](https://gitlab.staging.haskell.org/ghc/ghc/issues/16188)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      highest
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      [
https://gitlab.haskell.org/ghc/ghc/merge\_requests/207](https://gitlab.haskell.org/ghc/ghc/merge_requests/207)
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      goldfire
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#16204](https://gitlab.staging.haskell.org/ghc/ghc/issues/16204)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [GHC HEAD-only Core Lint error (Argument value doesn't match argument type)](https://gitlab.staging.haskell.org/ghc/ghc/issues/16204)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      highest
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      [
https://gitlab.haskell.org/ghc/ghc/merge\_requests/207](https://gitlab.haskell.org/ghc/ghc/merge_requests/207)
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#16218](https://gitlab.staging.haskell.org/ghc/ghc/issues/16218)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [T16180 is broken on Darwin](https://gitlab.staging.haskell.org/ghc/ghc/issues/16218)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      highest
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      [
https://gitlab.haskell.org/ghc/ghc/merge\_requests/195](https://gitlab.haskell.org/ghc/ghc/merge_requests/195)
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#16225](https://gitlab.staging.haskell.org/ghc/ghc/issues/16225)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [GHC HEAD-only Core Lint error (Trans coercion mis-match)](https://gitlab.staging.haskell.org/ghc/ghc/issues/16225)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      highest
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      [
https://gitlab.haskell.org/ghc/ghc/merge\_requests/207](https://gitlab.haskell.org/ghc/ghc/merge_requests/207)
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#11126](https://gitlab.staging.haskell.org/ghc/ghc/issues/11126)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Entered absent arg in a Repa program](https://gitlab.staging.haskell.org/ghc/ghc/issues/11126)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      high
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      [ Phab:D3221](https://phabricator.haskell.org/D3221)
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      bgamari
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#16195](https://gitlab.staging.haskell.org/ghc/ghc/issues/16195)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Program with trivial polymorphism leads to out of scope dictionary](https://gitlab.staging.haskell.org/ghc/ghc/issues/16195)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      high
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#9832](https://gitlab.staging.haskell.org/ghc/ghc/issues/9832)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      task
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Get rid of PERL dependency of \`ghc-split\`](https://gitlab.staging.haskell.org/ghc/ghc/issues/9832)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      [ Phab:D2768](https://phabricator.haskell.org/D2768)
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      dobenour
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#10857](https://gitlab.staging.haskell.org/ghc/ghc/issues/10857)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      ["ghci -XMonomorphismRestriction" doesn't turn on the monomorphism restriction](https://gitlab.staging.haskell.org/ghc/ghc/issues/10857)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      Gitlab Merge Request 35
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      RolandSenn
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#15656](https://gitlab.staging.haskell.org/ghc/ghc/issues/15656)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      task
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Extend -Wall with incomplete-uni-patterns and incomplete-record-updates](https://gitlab.staging.haskell.org/ghc/ghc/issues/15656)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      [ Phab:D5415](https://phabricator.haskell.org/D5415)
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      ckoparkar
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#15896](https://gitlab.staging.haskell.org/ghc/ghc/issues/15896)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      task
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [GHC API: add function to allow looking up Name for Located RdrName](https://gitlab.staging.haskell.org/ghc/ghc/issues/15896)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      [ Phab:D5330](https://phabricator.haskell.org/D5330)
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      alanz
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#16001](https://gitlab.staging.haskell.org/ghc/ghc/issues/16001)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [hadrian doesn't support setting intree gmp configuration explicitly](https://gitlab.staging.haskell.org/ghc/ghc/issues/16001)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      [ Phab:D5417](https://phabricator.haskell.org/D5417)
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
          </td>
<th>
            
    

## Status: upstream (5 matches)


  
          </th>
<td>
        </td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td></tr>
<tr><td>
      </td>
<th>
        
        Ticket (Ticket query: status: merge, status: patch, status: upstream,
milestone: 8.8.1, group: status, max: 0, col: id, col: type, col: summary,
col: priority, col: differential, col: owner, order: id)
      </th>
<th>
        
        Type (Ticket query: status: merge, status: patch, status: upstream,
milestone: 8.8.1, group: status, max: 0, col: id, col: type, col: summary,
col: priority, col: differential, col: owner, order: type)
      </th>
<th>
        
        Summary (Ticket query: status: merge, status: patch, status: upstream,
milestone: 8.8.1, group: status, max: 0, col: id, col: type, col: summary,
col: priority, col: differential, col: owner, order: summary)
      </th>
<th>
        
        Priority (Ticket query: status: merge, status: patch, status: upstream,
milestone: 8.8.1, group: status, max: 0, col: id, col: type, col: summary,
col: priority, col: differential, col: owner, desc: 1, order: priority)
      </th>
<th>
        
        Differential Rev(s) (Ticket query: status: merge, status: patch,
status: upstream, milestone: 8.8.1, group: status, max: 0, col: id, col: type,
col: summary, col: priority, col: differential, col: owner, order: differential)
      </th>
<th>
        
        Owner (Ticket query: status: merge, status: patch, status: upstream,
milestone: 8.8.1, group: status, max: 0, col: id, col: type, col: summary,
col: priority, col: differential, col: owner, order: owner)
      </th>
<td>
    </td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#13897](https://gitlab.staging.haskell.org/ghc/ghc/issues/13897)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      task
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Ship check-ppr in bindist and compile during testsuite run](https://gitlab.staging.haskell.org/ghc/ghc/issues/13897)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      high
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      [ Phab:D4039](https://phabricator.haskell.org/D4039)
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      alanz
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#9775](https://gitlab.staging.haskell.org/ghc/ghc/issues/9775)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      ["Failed to remove" errors during Windows build from hsc2hs](https://gitlab.staging.haskell.org/ghc/ghc/issues/9775)
                      
                      
                      
                      
                      
                      
                      
                      
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
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#10822](https://gitlab.staging.haskell.org/ghc/ghc/issues/10822)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [GHC inconsistently handles \\\\?\\ for long paths on Windows](https://gitlab.staging.haskell.org/ghc/ghc/issues/10822)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      [ Phab:D4416](https://phabricator.haskell.org/D4416)
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#12965](https://gitlab.staging.haskell.org/ghc/ghc/issues/12965)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [String merging broken on Windows](https://gitlab.staging.haskell.org/ghc/ghc/issues/12965)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      [ Phab:D3384](https://phabricator.haskell.org/D3384)
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#15074](https://gitlab.staging.haskell.org/ghc/ghc/issues/15074)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Possible uninitialised values in ffi64.c](https://gitlab.staging.haskell.org/ghc/ghc/issues/15074)
                      
                      
                      
                      
                      
                      
                      
                      
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
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr></table>


  



### new




  
  
  
  
  
    

## Status: new (35 matches)


  
  

<table><tr><td>
      </td>
<th>
        
        Ticket (Ticket query: status: new, milestone: 8.8.1, group: status,
max: 0, col: id, col: type, col: summary, col: priority, col: owner, order: id)
      </th>
<th>
        
        Type (Ticket query: status: new, milestone: 8.8.1, group: status,
max: 0, col: id, col: type, col: summary, col: priority, col: owner,
order: type)
      </th>
<th>
        
        Summary (Ticket query: status: new, milestone: 8.8.1, group: status,
max: 0, col: id, col: type, col: summary, col: priority, col: owner,
order: summary)
      </th>
<th>
        
        Priority (Ticket query: status: new, milestone: 8.8.1, group: status,
max: 0, col: id, col: type, col: summary, col: priority, col: owner, desc: 1,
order: priority)
      </th>
<th>
        
        Owner (Ticket query: status: new, milestone: 8.8.1, group: status,
max: 0, col: id, col: type, col: summary, col: priority, col: owner,
order: owner)
      </th>
<td>
    </td>
<td></td>
<td></td>
<td></td>
<td></td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#14375](https://gitlab.staging.haskell.org/ghc/ghc/issues/14375)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Implement with\# primop](https://gitlab.staging.haskell.org/ghc/ghc/issues/14375)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      highest
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      bgamari
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#15064](https://gitlab.staging.haskell.org/ghc/ghc/issues/15064)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [T8089 mysteriously fails when GHC is built with LLVM](https://gitlab.staging.haskell.org/ghc/ghc/issues/15064)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      highest
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      osa1
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#15779](https://gitlab.staging.haskell.org/ghc/ghc/issues/15779)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      task
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Follow-ups to D5169](https://gitlab.staging.haskell.org/ghc/ghc/issues/15779)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      highest
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#15919](https://gitlab.staging.haskell.org/ghc/ghc/issues/15919)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      task
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Deprecate split objects](https://gitlab.staging.haskell.org/ghc/ghc/issues/15919)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      highest
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#15948](https://gitlab.staging.haskell.org/ghc/ghc/issues/15948)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Hadrian build fails on Windows when invoked without --configure flag](https://gitlab.staging.haskell.org/ghc/ghc/issues/15948)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      highest
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#16051](https://gitlab.staging.haskell.org/ghc/ghc/issues/16051)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Cross compilation broken under Hadrian](https://gitlab.staging.haskell.org/ghc/ghc/issues/16051)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      highest
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#16058](https://gitlab.staging.haskell.org/ghc/ghc/issues/16058)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [GHC built on macOS Mojave nondeterministically segfaults](https://gitlab.staging.haskell.org/ghc/ghc/issues/16058)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      highest
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#16201](https://gitlab.staging.haskell.org/ghc/ghc/issues/16201)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [ghci063 failing on Darwin](https://gitlab.staging.haskell.org/ghc/ghc/issues/16201)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      highest
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#8095](https://gitlab.staging.haskell.org/ghc/ghc/issues/8095)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [TypeFamilies painfully slow](https://gitlab.staging.haskell.org/ghc/ghc/issues/8095)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      high
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      goldfire
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#8949](https://gitlab.staging.haskell.org/ghc/ghc/issues/8949)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [switch -msse2 to be on by default](https://gitlab.staging.haskell.org/ghc/ghc/issues/8949)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      high
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#12758](https://gitlab.staging.haskell.org/ghc/ghc/issues/12758)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      task
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Bring sanity to our performance testsuite](https://gitlab.staging.haskell.org/ghc/ghc/issues/12758)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      high
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#13253](https://gitlab.staging.haskell.org/ghc/ghc/issues/13253)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Exponential compilation time with RWST & ReaderT stack with \`-02\`](https://gitlab.staging.haskell.org/ghc/ghc/issues/13253)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      high
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      bgamari, osa1
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#13786](https://gitlab.staging.haskell.org/ghc/ghc/issues/13786)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [GHCi linker is dependent upon object file order](https://gitlab.staging.haskell.org/ghc/ghc/issues/13786)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      high
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#14069](https://gitlab.staging.haskell.org/ghc/ghc/issues/14069)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [RTS linker maps code as writable](https://gitlab.staging.haskell.org/ghc/ghc/issues/14069)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      high
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#14974](https://gitlab.staging.haskell.org/ghc/ghc/issues/14974)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [2-fold memory usage regression GHC 8.2.2 -\> GHC 8.4.1 compiling \`mmark\` package](https://gitlab.staging.haskell.org/ghc/ghc/issues/14974)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      high
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      davide
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#15059](https://gitlab.staging.haskell.org/ghc/ghc/issues/15059)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [ghcpkg05 fails](https://gitlab.staging.haskell.org/ghc/ghc/issues/15059)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      high
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#15262](https://gitlab.staging.haskell.org/ghc/ghc/issues/15262)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [GHC and iserv cannot agree on what an Integer is; insanity ensues](https://gitlab.staging.haskell.org/ghc/ghc/issues/15262)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      high
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#15383](https://gitlab.staging.haskell.org/ghc/ghc/issues/15383)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [T3171 doesn't terminate with Interrupted message on Darwin](https://gitlab.staging.haskell.org/ghc/ghc/issues/15383)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      high
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#15442](https://gitlab.staging.haskell.org/ghc/ghc/issues/15442)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [GhcStage3HcOpts passed to ghc-stage1](https://gitlab.staging.haskell.org/ghc/ghc/issues/15442)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      high
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#15503](https://gitlab.staging.haskell.org/ghc/ghc/issues/15503)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [interpreter: sequence\_ (replicate 100000000 (return ()))  gobbles up memory](https://gitlab.staging.haskell.org/ghc/ghc/issues/15503)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      high
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      osa1
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#15577](https://gitlab.staging.haskell.org/ghc/ghc/issues/15577)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [TypeApplications-related infinite loop (GHC 8.6+ only)](https://gitlab.staging.haskell.org/ghc/ghc/issues/15577)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      high
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#15703](https://gitlab.staging.haskell.org/ghc/ghc/issues/15703)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Significant compilation time blowup when refactoring singletons-heavy code](https://gitlab.staging.haskell.org/ghc/ghc/issues/15703)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      high
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#15971](https://gitlab.staging.haskell.org/ghc/ghc/issues/15971)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Hadrian fails Shake's linter on Windows](https://gitlab.staging.haskell.org/ghc/ghc/issues/15971)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      high
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#15982](https://gitlab.staging.haskell.org/ghc/ghc/issues/15982)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Hadrian's \`--configure\` flag is broken on Windows](https://gitlab.staging.haskell.org/ghc/ghc/issues/15982)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      high
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#16037](https://gitlab.staging.haskell.org/ghc/ghc/issues/16037)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [memcpy test inexplicably failing](https://gitlab.staging.haskell.org/ghc/ghc/issues/16037)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      high
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#16073](https://gitlab.staging.haskell.org/ghc/ghc/issues/16073)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Hadrian build fails on Windows](https://gitlab.staging.haskell.org/ghc/ghc/issues/16073)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      high
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#16085](https://gitlab.staging.haskell.org/ghc/ghc/issues/16085)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [ffi018\_ghci fails when unregisterised](https://gitlab.staging.haskell.org/ghc/ghc/issues/16085)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      high
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#16112](https://gitlab.staging.haskell.org/ghc/ghc/issues/16112)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [T11334b fails in the devel2 way](https://gitlab.staging.haskell.org/ghc/ghc/issues/16112)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      high
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#16113](https://gitlab.staging.haskell.org/ghc/ghc/issues/16113)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [T14740 fails in debugged compiler](https://gitlab.staging.haskell.org/ghc/ghc/issues/16113)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      high
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#15990](https://gitlab.staging.haskell.org/ghc/ghc/issues/15990)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Dynamically built GHC crashes on MacOS](https://gitlab.staging.haskell.org/ghc/ghc/issues/15990)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#16165](https://gitlab.staging.haskell.org/ghc/ghc/issues/16165)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      task
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Move Hadrian (github) wiki information to in-tree docs](https://gitlab.staging.haskell.org/ghc/ghc/issues/16165)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#16212](https://gitlab.staging.haskell.org/ghc/ghc/issues/16212)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [API Annotations: Parens not attached correctly for ClassDecl](https://gitlab.staging.haskell.org/ghc/ghc/issues/16212)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      alanz
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#16217](https://gitlab.staging.haskell.org/ghc/ghc/issues/16217)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      task
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [check-api-annotations should check that an annotation does not precede its span](https://gitlab.staging.haskell.org/ghc/ghc/issues/16217)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#16230](https://gitlab.staging.haskell.org/ghc/ghc/issues/16230)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [API Annotations: more explicit foralls fixup](https://gitlab.staging.haskell.org/ghc/ghc/issues/16230)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      alanz
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#16236](https://gitlab.staging.haskell.org/ghc/ghc/issues/16236)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [API Annotations: AnnAt disconnected for TYPEAPP](https://gitlab.staging.haskell.org/ghc/ghc/issues/16236)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      alanz
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr></table>


  



### infoneeded




  
  
  
  
  
    
  
  

<table><tr><td>
      </td>
<th>
        
        Ticket (Ticket query: status: infoneeded, milestone: 8.8.1,
group: status, max: 0, col: id, col: type, col: summary, col: priority,
col: owner, order: id)
      </th>
<th>
        
        Type (Ticket query: status: infoneeded, milestone: 8.8.1, group: status,
max: 0, col: id, col: type, col: summary, col: priority, col: owner,
order: type)
      </th>
<th>
        
        Summary (Ticket query: status: infoneeded, milestone: 8.8.1,
group: status, max: 0, col: id, col: type, col: summary, col: priority,
col: owner, order: summary)
      </th>
<th>
        
        Priority (Ticket query: status: infoneeded, milestone: 8.8.1,
group: status, max: 0, col: id, col: type, col: summary, col: priority,
col: owner, desc: 1, order: priority)
      </th>
<th>
        
        Owner (Ticket query: status: infoneeded, milestone: 8.8.1,
group: status, max: 0, col: id, col: type, col: summary, col: priority,
col: owner, order: owner)
      </th>
<td>
    </td></tr>
<tr><td>
          </td>
<th>
            No tickets found
          </th>
<td>
        </td>
<td></td>
<td></td>
<td></td>
<td></td></tr></table>


  



