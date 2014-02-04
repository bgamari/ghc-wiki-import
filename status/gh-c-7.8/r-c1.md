
**Known issues**:


- [\#8696](https://gitlab.staging.haskell.org/ghc/ghc/issues/8696) - `lens` fails to build. 
- Mavericks suffers from some preprocessing bugs. We're going to try using `cpphs` as an alternative.
- [\#7602](https://gitlab.staging.haskell.org/ghc/ghc/issues/7602) - OS X's parallel garbage collector is still performing badly. We're going to keep looking at this for the final release.
- RC1's version number is 7.8.20140130 when it should be 7.8.**0**.20140130. RC2 will use 7.8.0.\<date\>.
- The Linux binary builds require glibc 2.15 (Ubuntu 12.04.) RC2 will use glibc 2.13 (Debian 7/stable) instead.


** Tickets filed against RC1 **




  
  
  
  
  
    
  
  

<table><tr><td>
      </td>
<th>
        
        Ticket (Ticket query: version: 7.8.1-rc1, max: 0, col: id, col: type,
col: summary, col: priority, col: owner, desc: 1, order: id)
      </th>
<th>
        
        Type (Ticket query: version: 7.8.1-rc1, max: 0, col: id, col: type,
col: summary, col: priority, col: owner, order: type)
      </th>
<th>
        
        Summary (Ticket query: version: 7.8.1-rc1, max: 0, col: id, col: type,
col: summary, col: priority, col: owner, order: summary)
      </th>
<th>
        
        Priority (Ticket query: version: 7.8.1-rc1, max: 0, col: id, col: type,
col: summary, col: priority, col: owner, order: priority)
      </th>
<th>
        
        Owner (Ticket query: version: 7.8.1-rc1, max: 0, col: id, col: type,
col: summary, col: priority, col: owner, order: owner)
      </th>
<td>
    </td>
<td></td>
<td></td>
<td></td>
<td></td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#5013](https://gitlab.staging.haskell.org/ghc/ghc/issues/5013)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [sporadic failures during compilation under solaris](https://gitlab.staging.haskell.org/ghc/ghc/issues/5013)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#5682](https://gitlab.staging.haskell.org/ghc/ghc/issues/5682)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Properly parse promoted data constructor operators](https://gitlab.staging.haskell.org/ghc/ghc/issues/5682)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#7134](https://gitlab.staging.haskell.org/ghc/ghc/issues/7134)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [ghc-7.6.0.20120810-x86\_64-windows.exe -\> internal error R\_X86\_64\_PC32](https://gitlab.staging.haskell.org/ghc/ghc/issues/7134)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      highest
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      thoughtpolice
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#7655](https://gitlab.staging.haskell.org/ghc/ghc/issues/7655)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [7.6.2 Segmentation Fault/Bus Error in large exponentation](https://gitlab.staging.haskell.org/ghc/ghc/issues/7655)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      highest
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      rwbarton
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#7830](https://gitlab.staging.haskell.org/ghc/ghc/issues/7830)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Error: operand out of range](https://gitlab.staging.haskell.org/ghc/ghc/issues/7830)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8631](https://gitlab.staging.haskell.org/ghc/ghc/issues/8631)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Need ImpredicativeTypes for GeneralizedNewtypeDeriving?](https://gitlab.staging.haskell.org/ghc/ghc/issues/8631)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8698](https://gitlab.staging.haskell.org/ghc/ghc/issues/8698)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [.ctors handling does not work on Windows 64-bit ghci](https://gitlab.staging.haskell.org/ghc/ghc/issues/8698)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8699](https://gitlab.staging.haskell.org/ghc/ghc/issues/8699)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Multiple test faliures on 32-bit Linux systems.](https://gitlab.staging.haskell.org/ghc/ghc/issues/8699)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8700](https://gitlab.staging.haskell.org/ghc/ghc/issues/8700)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Cross-compilation perf-cross BuildFlavour](https://gitlab.staging.haskell.org/ghc/ghc/issues/8700)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8701](https://gitlab.staging.haskell.org/ghc/ghc/issues/8701)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Update libffi-tarballs to latest libffi](https://gitlab.staging.haskell.org/ghc/ghc/issues/8701)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8705](https://gitlab.staging.haskell.org/ghc/ghc/issues/8705)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Type inference regression with local dictionaries](https://gitlab.staging.haskell.org/ghc/ghc/issues/8705)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8706](https://gitlab.staging.haskell.org/ghc/ghc/issues/8706)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Kind operators not parsed](https://gitlab.staging.haskell.org/ghc/ghc/issues/8706)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8717](https://gitlab.staging.haskell.org/ghc/ghc/issues/8717)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Segfault in 64-bit Windows GHCi](https://gitlab.staging.haskell.org/ghc/ghc/issues/8717)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8719](https://gitlab.staging.haskell.org/ghc/ghc/issues/8719)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [clarify prefetch release notes + remove some deadcode](https://gitlab.staging.haskell.org/ghc/ghc/issues/8719)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8722](https://gitlab.staging.haskell.org/ghc/ghc/issues/8722)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [powerpc64: error Cannot find a way to declare the thread-local gc variable](https://gitlab.staging.haskell.org/ghc/ghc/issues/8722)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      thoughtpolice
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#8724](https://gitlab.staging.haskell.org/ghc/ghc/issues/8724)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [x86\_64 cas references unsigned int rather than StgWord](https://gitlab.staging.haskell.org/ghc/ghc/issues/8724)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8726](https://gitlab.staging.haskell.org/ghc/ghc/issues/8726)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [integer-gmp division regression](https://gitlab.staging.haskell.org/ghc/ghc/issues/8726)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      highest
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      hvr
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#8728](https://gitlab.staging.haskell.org/ghc/ghc/issues/8728)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Segmentation fault in Cabal](https://gitlab.staging.haskell.org/ghc/ghc/issues/8728)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8731](https://gitlab.staging.haskell.org/ghc/ghc/issues/8731)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [long compilation time for module with large data type and partial record selectors](https://gitlab.staging.haskell.org/ghc/ghc/issues/8731)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8734](https://gitlab.staging.haskell.org/ghc/ghc/issues/8734)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [7.8.1 rc1 ghci won't load compiled files](https://gitlab.staging.haskell.org/ghc/ghc/issues/8734)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8735](https://gitlab.staging.haskell.org/ghc/ghc/issues/8735)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [hpc crashes on platforms using dynamic linking](https://gitlab.staging.haskell.org/ghc/ghc/issues/8735)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8737](https://gitlab.staging.haskell.org/ghc/ghc/issues/8737)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [T5975a fails when using official Windows Python distribution](https://gitlab.staging.haskell.org/ghc/ghc/issues/8737)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8739](https://gitlab.staging.haskell.org/ghc/ghc/issues/8739)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [($) returning kind \# no longer type checks](https://gitlab.staging.haskell.org/ghc/ghc/issues/8739)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8741](https://gitlab.staging.haskell.org/ghc/ghc/issues/8741)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [\`System.Directory.getPermissions\` fails on read-only filesystem](https://gitlab.staging.haskell.org/ghc/ghc/issues/8741)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      high
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      AlainODea
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#8743](https://gitlab.staging.haskell.org/ghc/ghc/issues/8743)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [The impossible happened : Prelude.(!!): index too large](https://gitlab.staging.haskell.org/ghc/ghc/issues/8743)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      highest
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      nomeata
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#8744](https://gitlab.staging.haskell.org/ghc/ghc/issues/8744)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      ["Thread-local storage not supported" for Mac](https://gitlab.staging.haskell.org/ghc/ghc/issues/8744)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8745](https://gitlab.staging.haskell.org/ghc/ghc/issues/8745)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [GeneralizedNewtypeDeriving is still not Safe](https://gitlab.staging.haskell.org/ghc/ghc/issues/8745)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8746](https://gitlab.staging.haskell.org/ghc/ghc/issues/8746)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Crosscompiling](https://gitlab.staging.haskell.org/ghc/ghc/issues/8746)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8748](https://gitlab.staging.haskell.org/ghc/ghc/issues/8748)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [ghc-7.8-rc1/HEAD: --enable-unregisterised fails to build and run with threaded RTS, profiling mode](https://gitlab.staging.haskell.org/ghc/ghc/issues/8748)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8749](https://gitlab.staging.haskell.org/ghc/ghc/issues/8749)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Pattern synonyms crash GHCi](https://gitlab.staging.haskell.org/ghc/ghc/issues/8749)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8754](https://gitlab.staging.haskell.org/ghc/ghc/issues/8754)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [:set +s always says space usage is 0 bytes](https://gitlab.staging.haskell.org/ghc/ghc/issues/8754)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8757](https://gitlab.staging.haskell.org/ghc/ghc/issues/8757)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Disallow local pattern synonym declarations](https://gitlab.staging.haskell.org/ghc/ghc/issues/8757)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      cactus
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#8758](https://gitlab.staging.haskell.org/ghc/ghc/issues/8758)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [GeneralizedNewtypeDeriving sometimes needs RankNTypes](https://gitlab.staging.haskell.org/ghc/ghc/issues/8758)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
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
<th>[\#8759](https://gitlab.staging.haskell.org/ghc/ghc/issues/8759)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Pattern synonyms and TH](https://gitlab.staging.haskell.org/ghc/ghc/issues/8759)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
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
<th>[\#8760](https://gitlab.staging.haskell.org/ghc/ghc/issues/8760)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [ghc 7.8: ghc-split not installed](https://gitlab.staging.haskell.org/ghc/ghc/issues/8760)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8762](https://gitlab.staging.haskell.org/ghc/ghc/issues/8762)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Panic involving unboxed tuples and phantom types](https://gitlab.staging.haskell.org/ghc/ghc/issues/8762)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8764](https://gitlab.staging.haskell.org/ghc/ghc/issues/8764)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Usage of \`sed' in GHC build system (Solaris build failure).](https://gitlab.staging.haskell.org/ghc/ghc/issues/8764)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8766](https://gitlab.staging.haskell.org/ghc/ghc/issues/8766)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [length \[Integer\] is twice as slow but length \[Int\] is 10 times faster](https://gitlab.staging.haskell.org/ghc/ghc/issues/8766)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8770](https://gitlab.staging.haskell.org/ghc/ghc/issues/8770)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [ghci dynamic loading on MacOSX assumes .dylib extension (should check .so also)](https://gitlab.staging.haskell.org/ghc/ghc/issues/8770)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8773](https://gitlab.staging.haskell.org/ghc/ghc/issues/8773)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Require -XIncoherentInstances to write role annotations on class definitions](https://gitlab.staging.haskell.org/ghc/ghc/issues/8773)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
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
<th>[\#8775](https://gitlab.staging.haskell.org/ghc/ghc/issues/8775)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [GHC panic building postgresql-simple](https://gitlab.staging.haskell.org/ghc/ghc/issues/8775)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8778](https://gitlab.staging.haskell.org/ghc/ghc/issues/8778)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      feature request
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Typeable TypeNats](https://gitlab.staging.haskell.org/ghc/ghc/issues/8778)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      diatchki
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#8781](https://gitlab.staging.haskell.org/ghc/ghc/issues/8781)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      feature request
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [check if GNU nm is really needed and if so let configure detect gnm](https://gitlab.staging.haskell.org/ghc/ghc/issues/8781)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      kgardas
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#8786](https://gitlab.staging.haskell.org/ghc/ghc/issues/8786)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [libraries/primitive build failure on Solaris 10](https://gitlab.staging.haskell.org/ghc/ghc/issues/8786)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8792](https://gitlab.staging.haskell.org/ghc/ghc/issues/8792)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [doc incorrect: Mac OS X does not supply lllvm i.e. opt and llc](https://gitlab.staging.haskell.org/ghc/ghc/issues/8792)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8794](https://gitlab.staging.haskell.org/ghc/ghc/issues/8794)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      task
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Unresolved \@ArSupportsAtFile@ on Solaris distribution.](https://gitlab.staging.haskell.org/ghc/ghc/issues/8794)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      lowest
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      kgardas
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#8795](https://gitlab.staging.haskell.org/ghc/ghc/issues/8795)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Build fails on Solaris 10 due to missing ranlib](https://gitlab.staging.haskell.org/ghc/ghc/issues/8795)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      kgardas
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#8797](https://gitlab.staging.haskell.org/ghc/ghc/issues/8797)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      feature request
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Generics instances for monoid and applicative newtypes](https://gitlab.staging.haskell.org/ghc/ghc/issues/8797)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8800](https://gitlab.staging.haskell.org/ghc/ghc/issues/8800)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [hpc dyld issue](https://gitlab.staging.haskell.org/ghc/ghc/issues/8800)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8801](https://gitlab.staging.haskell.org/ghc/ghc/issues/8801)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Exclude extralibs from sdist](https://gitlab.staging.haskell.org/ghc/ghc/issues/8801)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8803](https://gitlab.staging.haskell.org/ghc/ghc/issues/8803)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [ghc panic ((!!): index too large) when building some packages](https://gitlab.staging.haskell.org/ghc/ghc/issues/8803)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8805](https://gitlab.staging.haskell.org/ghc/ghc/issues/8805)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [QuasiQuote should imply -dynamic-too, too](https://gitlab.staging.haskell.org/ghc/ghc/issues/8805)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8808](https://gitlab.staging.haskell.org/ghc/ghc/issues/8808)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [ImpredicativeTypes type checking fails depending on syntax of arguments](https://gitlab.staging.haskell.org/ghc/ghc/issues/8808)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8810](https://gitlab.staging.haskell.org/ghc/ghc/issues/8810)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [rts/RetainerProfile.c: include missing header for 'markStableTables'](https://gitlab.staging.haskell.org/ghc/ghc/issues/8810)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8813](https://gitlab.staging.haskell.org/ghc/ghc/issues/8813)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      task
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [further support deriving instances Typeable1, Typeable2, etc](https://gitlab.staging.haskell.org/ghc/ghc/issues/8813)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      dreixel
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#8814](https://gitlab.staging.haskell.org/ghc/ghc/issues/8814)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [7.8 optimizes attoparsec improperly](https://gitlab.staging.haskell.org/ghc/ghc/issues/8814)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8815](https://gitlab.staging.haskell.org/ghc/ghc/issues/8815)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [confusing language in error message](https://gitlab.staging.haskell.org/ghc/ghc/issues/8815)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      normal
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      sivteck
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#8817](https://gitlab.staging.haskell.org/ghc/ghc/issues/8817)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [segmentation fault in 7.8 RC1](https://gitlab.staging.haskell.org/ghc/ghc/issues/8817)
                      
                      
                      
                      
                      
                      
                      
                      
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      highest
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      simonmar
                      
                      
                      
                      
                    </th>
<td>
                  
                
              </td></tr>
<tr><td>
                
                  
                    </td>
<th>[\#8820](https://gitlab.staging.haskell.org/ghc/ghc/issues/8820)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [7.8 RC1 unregisterised fails selfbootstrap on 64 bit Linux](https://gitlab.staging.haskell.org/ghc/ghc/issues/8820)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8823](https://gitlab.staging.haskell.org/ghc/ghc/issues/8823)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [showFloat for higher precision types produces strange results for some values](https://gitlab.staging.haskell.org/ghc/ghc/issues/8823)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8825](https://gitlab.staging.haskell.org/ghc/ghc/issues/8825)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [ghc can't determine gcc version on ru\_RU locale](https://gitlab.staging.haskell.org/ghc/ghc/issues/8825)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8918](https://gitlab.staging.haskell.org/ghc/ghc/issues/8918)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Network package doesn't load under GHC 7.8 RC on windows (?)](https://gitlab.staging.haskell.org/ghc/ghc/issues/8918)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8946](https://gitlab.staging.haskell.org/ghc/ghc/issues/8946)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [Using hdevtools caused "Evaluated the place holder for a PostTcKind"](https://gitlab.staging.haskell.org/ghc/ghc/issues/8946)
                      
                      
                      
                      
                      
                      
                      
                      
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
<th>[\#8951](https://gitlab.staging.haskell.org/ghc/ghc/issues/8951)</th>
<td>
                    
                  
                
                  
                    
                    </td>
<th>
                      
                      
                      
                      
                      
                      
                      
                      
                      bug
                    </th>
<td>
                  
                
                  
                    
                    </td>
<th>
                      [genSym uses atomic\_inc but doesn't link arm\_atomic\_spin\_lock](https://gitlab.staging.haskell.org/ghc/ghc/issues/8951)
                      
                      
                      
                      
                      
                      
                      
                      
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
                  
                
              </td></tr></table>


  



