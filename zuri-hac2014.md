# Bug squashing at ZuriHac2014



Joachim (nomeata) wants to run a small bugsquashing sprint at [
ZuriHac 2014](http://www.haskell.org/haskellwiki/ZuriHac2014/Projects). 


## Requirements



You should bring some Haskell experience and be confident reading other people’s Haskell code. You do not need to know all the latest fancy type hackery – GHC itself is written in quite plain Haskell. Some knowledge of git is also useful.



Obviously, you need a machine to work on. The more core it has, the less you’ll have to wait.


## Setup



If you want to join in, you can come prepared:


- Read through [Newcomers](newcomers)
- Get an account on this trac.
- Make sure that you have built GHC once yourself.
- Your changes need to be validated. So make sure you validated GHC once. I suggest to have a second working copy of GHC that you only use to validate. There is a [section](working-conventions/git#workflow-with-validate) explaining how to do this.
- Fork [
  ghc on github](https://github.com/ghc/ghc/) (or otherwise publish a fork of the GHC repo) for easier collaboration during the hackathon.

## Optional tips



If you have a strong remote machine with lots of cores, you can have the validate tree remotely.



For more convenient validation, especially if the validate repository is remotely, I (Joachim) have a script `ci-validate.sh` that waits for a new branch calls `validate/foo`, then validates it cleanly and either moves it to `validated/foo` or `broken/foo`. If you want to set up that as well, fetch the script from my [
ghc-devscripts repository](https://github.com/nomeata/ghc-devscripts).


## Possible tickets



This is a list of tickets that might be suitable for a hacking sprint, but feel free to look for others (click “All Bugs“ and “All Tasks” on the left). And of course, feel free to extend this list.




  
  
  
  
  
    
  
  

<table><tr><td>
      </td>
<th>
        
        Ticket (Ticket query:
id: 9095%2C9122%2C9127%2C9132%2C9136%2C95%2C1388%2C8959%2C9156%2C17, max: 0,
desc: 1, order: id)
      </th>
<th>
        
        Summary (Ticket query:
id: 9095%2C9122%2C9127%2C9132%2C9136%2C95%2C1388%2C8959%2C9156%2C17, max: 0,
order: summary)
      </th>
<th>
        
        Owner (Ticket query:
id: 9095%2C9122%2C9127%2C9132%2C9136%2C95%2C1388%2C8959%2C9156%2C17, max: 0,
order: owner)
      </th>
<th>
        
        Type (Ticket query:
id: 9095%2C9122%2C9127%2C9132%2C9136%2C95%2C1388%2C8959%2C9156%2C17, max: 0,
order: type)
      </th>
<th>
        
        Status (Ticket query:
id: 9095%2C9122%2C9127%2C9132%2C9136%2C95%2C1388%2C8959%2C9156%2C17, max: 0,
order: status)
      </th>
<th>
        
        Priority (Ticket query:
id: 9095%2C9122%2C9127%2C9132%2C9136%2C95%2C1388%2C8959%2C9156%2C17, max: 0,
order: priority)
      </th>
<th>
        
        Milestone (Ticket query:
id: 9095%2C9122%2C9127%2C9132%2C9136%2C95%2C1388%2C8959%2C9156%2C17, max: 0,
order: milestone)
      </th>
<td>
    </td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td>
<td></td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#17](https://gitlab.staging.haskell.org/ghc/ghc/issues/17)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      [Separate warnings for unused local and top-level bindings](https://gitlab.staging.haskell.org/ghc/ghc/issues/17)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      feature request
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      closed
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      lowest
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      [8.0.1](/trac/ghc/milestone/8.0.1)
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#95](https://gitlab.staging.haskell.org/ghc/ghc/issues/95)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      [GHCi :edit command should jump to the the last error](https://gitlab.staging.haskell.org/ghc/ghc/issues/95)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      lortabac
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      feature request
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      closed
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      [⊥](/trac/ghc/milestone/%E2%8A%A5)
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#1388](https://gitlab.staging.haskell.org/ghc/ghc/issues/1388)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      [Newbie help features](https://gitlab.staging.haskell.org/ghc/ghc/issues/1388)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      feature request
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      closed
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      low
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      [⊥](/trac/ghc/milestone/%E2%8A%A5)
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#8959](https://gitlab.staging.haskell.org/ghc/ghc/issues/8959)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      [GHCi should honour UnicodeSyntax](https://gitlab.staging.haskell.org/ghc/ghc/issues/8959)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      closed
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      low
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      [8.0.1](/trac/ghc/milestone/8.0.1)
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#9095](https://gitlab.staging.haskell.org/ghc/ghc/issues/9095)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      [make sdist picks up test files](https://gitlab.staging.haskell.org/ghc/ghc/issues/9095)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      thomie
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      closed
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      low
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      [8.2.1](/trac/ghc/milestone/8.2.1)
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#9122](https://gitlab.staging.haskell.org/ghc/ghc/issues/9122)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      [Make Lint check for bad uses of \`unsafeCoerce\`](https://gitlab.staging.haskell.org/ghc/ghc/issues/9122)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      qnikst
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      closed
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      [8.0.1](/trac/ghc/milestone/8.0.1)
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#9127](https://gitlab.staging.haskell.org/ghc/ghc/issues/9127)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      [Don't warn about pattern-bindings of the form \`let !\_ = rhs\`](https://gitlab.staging.haskell.org/ghc/ghc/issues/9127)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      closed
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
<th>[\#9132](https://gitlab.staging.haskell.org/ghc/ghc/issues/9132)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      [takeWhile&C. still not fusible](https://gitlab.staging.haskell.org/ghc/ghc/issues/9132)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      skeuchel
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      closed
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      [7.10.1](/trac/ghc/milestone/7.10.1)
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#9136](https://gitlab.staging.haskell.org/ghc/ghc/issues/9136)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      [Constant folding in Core could be better](https://gitlab.staging.haskell.org/ghc/ghc/issues/9136)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      closed
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      [8.6.1](/trac/ghc/milestone/8.6.1)
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#9156](https://gitlab.staging.haskell.org/ghc/ghc/issues/9156)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      [Duplicate record field](https://gitlab.staging.haskell.org/ghc/ghc/issues/9156)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      gintas
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      closed
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
                  
                
              </td></tr></table>


  



## TODO (by Joachim)


- Make sure I have my validate machine up and running efficiently.


 


