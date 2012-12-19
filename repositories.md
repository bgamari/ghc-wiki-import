


# GHC Repositories



This page lists the active repositories relating to GHC. For instructions on actually getting a GHC source tree, see [Getting The Sources](building/getting-the-sources).



For read-only browsing, you can use the:


- [ Trac source browser](http://hackage.haskell.org/trac/ghc/browser)
- [ GitHub GHC mirror](http://github.com/ghc/ghc)
- [ GitWeb GHC browser](http://darcs.haskell.org/cgi-bin/gitweb.cgi)


GHC's repos use git; see [Git Working Conventions](working-conventions/git). For darcs-related stuff see [Darcs To Git](darcs-to-git) and [Git For Darcs Users](git-for-darcs-users).


## Overview



A GHC source tree is made of a collection of repositories. The script [sync-all](building/sync-all) knows how to apply git commands to the whole collection of repositories at once, for example to pull changes from the upstream repositories.



Here is a list of the repositories that GHC uses.  The columns have the following meaning


- **Location in tree**: where in the source tree this repository sits.
- **Upstream repo?**: if "yes", this library is maintained by someone else, 
  and its master repo is somewhere else.  See the [page about upstream repositories](repositories/upstream).
- **Reqd to build?**: is "no" is this library is not required to build GHC. We have a few of these because we use them for tests and suchlike.
- **Installed?**: is "no" if the library is not installed in a GHC installation. All others are installed with GHC. See the [libraries page](commentary/libraries) for more info.
- **GHC repo**: in every case there is a repo on `http://darcs.haskell.org/`, which contains the bits we use for building GHC every night. For libraries with upstream repos, this is just a lagging mirror of the master (see [upstream repositories](repositories/upstream))

<table><tr><th>**Location in tree**</th>
<td>   </td>
<th> **Upstream repo?**</th>
<td> </td>
<th>**Reqd to build?**</th>
<td>   </td>
<th>**Installed?**</th>
<td> </td>
<th>**GHC repo http://darcs.haskell.org/...**</th></tr>
<tr><th>. (ghc itself)</th>
<td>                    </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>ghc.git/</th></tr>
<tr><th>ghc-tarballs</th>
<td>                      </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th> no  </th>
<td> </td>
<th>ghc-tarballs.git/</th></tr>
<tr><th>utils/hsc2hs</th>
<td>                      </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>utils/hsc2hs.git/</th></tr>
<tr><th>utils/haddock</th>
<td>                     </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>haddock.git</th></tr>
<tr><th>testsuite</th>
<td>              	       </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>testsuite.git</th></tr>
<tr><th>nofib</th>
<td>                  	       </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>nofib.git</th></tr>
<tr><th>libraries/array</th>
<td>                   </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>packages/array.git/</th></tr>
<tr><th>libraries/base</th>
<td>                    </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>packages/base.git/</th></tr>
<tr><th>libraries/binary</th>
<td>                  </td>
<th> yes </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>packages/.git/</th></tr>
<tr><th>libraries/bytestring</th>
<td>              </td>
<th> yes </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>packages/bytestring.git/</th></tr>
<tr><th>libraries/Cabal</th>
<td>                   </td>
<th> yes </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>packages/Cabal.git/</th></tr>
<tr><th>libraries/containers</th>
<td>              </td>
<th> yes </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>packages/containers.git/</th></tr>
<tr><th>libraries/directory</th>
<td>               </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>packages/directory.git/</th></tr>
<tr><th>libraries/extensible-exceptions</th>
<td>   </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>packages/extensible-exceptions.git/</th></tr>
<tr><th>libraries/filepath</th>
<td>                </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>packages/filepath.git/</th></tr>
<tr><th>libraries/ghc-prim</th>
<td>                </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>packages/ghc-prim.git/</th></tr>
<tr><th>libraries/haskeline</th>
<td>               </td>
<th> yes </th>
<td> </td>
<th>     </th>
<td> </td>
<th> no  </th>
<td> </td>
<th>packages/haskeline.git/</th></tr>
<tr><th>libraries/haskell98</th>
<td>               </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>packages/haskell98.git/</th></tr>
<tr><th>libraries/haskell2010</th>
<td>             </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>packages/haskell2010.git/</th></tr>
<tr><th>libraries/hoopl</th>
<td>                   </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>packages/hoopl.git/</th></tr>
<tr><th>libraries/hpc</th>
<td>                     </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>packages/hpc.git/</th></tr>
<tr><th>libraries/integer-gmp</th>
<td>             </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>packages/integer-gmp.git/</th></tr>
<tr><th>libraries/integer-simple</th>
<td>          </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>packages/integer-simple.git/</th></tr>
<tr><th>libraries/mtl</th>
<td>                     </td>
<th> yes </th>
<td> </td>
<th> no  </th>
<td> </td>
<th> no  </th>
<td> </td>
<th>packages/mtl.git/</th></tr>
<tr><th>libraries/old-locale</th>
<td>              </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>packages/old-locale.git/</th></tr>
<tr><th>libraries/old-time</th>
<td>                </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>packages/old-time.git/</th></tr>
<tr><th>libraries/pretty</th>
<td>                  </td>
<th> yes </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>packages/pretty.git/</th></tr>
<tr><th>libraries/process</th>
<td>                 </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>packages/process.git/</th></tr>
<tr><th>libraries/random</th>
<td>                  </td>
<th> yes </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>packages/random.git/</th></tr>
<tr><th>libraries/template-haskell</th>
<td>        </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>packages/template-haskell.git/</th></tr>
<tr><th>libraries/terminfo</th>
<td>     	       </td>
<th> yes </th>
<td> </td>
<th>     </th>
<td> </td>
<th> no  </th>
<td> </td>
<th>packages/terminfo.git/</th></tr>
<tr><th>libraries/time</th>
<td>         	       </td>
<th> yes </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>packages/time.git/</th></tr>
<tr><th>libraries/transformers</th>
<td> 	       </td>
<th> yes </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>packages/transformers.git/</th></tr>
<tr><th>libraries/unix</th>
<td>         	       </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>packages/unix.git/</th></tr>
<tr><th>libraries/utf8-string</th>
<td>  	       </td>
<th> yes </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>packages/utf8-string.git/</th></tr>
<tr><th>libraries/Win32</th>
<td>	    	       </td>
<th> yes </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>packages/Win32.git/</th></tr>
<tr><th>libraries/xhtml</th>
<td>	    	       </td>
<th> yes </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>packages/xhtml.git/</th></tr>
<tr><th>libraries/primitive</th>
<td>       	       </td>
<th> yes </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>packages/primitive.git/</th></tr>
<tr><th>libraries/vector</th>
<td>       	       </td>
<th> yes </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>packages/vector.git/</th></tr>
<tr><th>libraries/dph</th>
<td>          	       </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>     </th>
<td> </td>
<th>packages/dph.git/</th></tr>
<tr><th>libraries/deepseq</th>
<td>      	       </td>
<th>     </th>
<td> </td>
<th> no  </th>
<td> </td>
<th> no </th>
<td> </td>
<th>packages/deepseq.git/</th></tr>
<tr><th>libraries/parallel</th>
<td>     	       </td>
<th>     </th>
<td> </td>
<th> no  </th>
<td> </td>
<th> no  </th>
<td> </td>
<th>packages/parallel.git/</th></tr>
<tr><th>libraries/stm</th>
<td>          	       </td>
<th>     </th>
<td> </td>
<th> no  </th>
<td> </td>
<th> no  </th>
<td> </td>
<th>packages/stm.git/</th></tr></table>


## The 'packages' file



The master list of repositories is in the file [packages](/trac/ghc/browser/ghc/packages), and this is where the `sync-all` script finds out about which repositories make up the complete tree.  It duplicates the information in the above table; indeed, it is really the authoritative version (so complain if the table and file differ!).



The "`tag`" in the master table in [packages](/trac/ghc/browser/ghc/packages) has the following significance:


- **"`-`"**: [boot libraries](commentary/libraries), necessary to build GHC
- **"`testsuite`"**: GHC's [regression tests](building/running-tests), not necessary for a build, but is necessary if you're working on GHC
- **"`nofib`"**: GHC's [nofib benchmark suite](building/running-no-fib)
- **"`dph`"**: packages for [Data Parallel Haskell](data-parallel), which is not shipped with GHC but we test all changes to GHC against these repositories so they are usually included in a checked-out source tree.
- **"`extra`"**: extra packages you might want to include in a build (the `parallel` package, for example), but aren't necessary to get a working GHC.


See the [Commentary/Libraries](commentary/libraries) page for more information about GHC's libraries.


## Modifying local packages



For libraries for which there no upstream repo, you can modify the GHC repo above directly.



When making a change to a library, you must also update the version
number if appropriate. Version number in the repositories should be
maintained such that, if the library were to be release as-is, then
they would have the correct version number. For example, if the last
release of a library was 1.2.0.3 and you remove a function from it
then, as per the
[
Package versioning policy](http://www.haskell.org/haskellwiki/Package_versioning_policy),
the version number should be bumped to 1.3.0.0. If it is already
1.3.0.0 or higher then no further change is necessary. In order to
make this easier, the version line in the `.cabal` file should be
followed by a comment such as


```wiki
-- GHC 7.6.1 released with 1.2.0.3
```

## Mirroring new packages to GitHub



Currently, all our repositories are being mirrored to GitHub by GitHub themselves. If you wish to add/remove a repository you need to email GitHub support at support@… and ask them to do it. Currently there is no way to administer this ourselves.


## Branches



For how we manage release branches, see [WorkingConventions/Releases](working-conventions/releases).



The following branches are active:


<table><tr><th>**7.4 Branch**</th>
<td>
To switch to this branch run:

```wiki
$ ./sync-all checkout ghc-7.4
```

</td></tr></table>


