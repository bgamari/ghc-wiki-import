


# GHC Repositories



This page lists the active repositories relating to GHC. These are Git repositories, so you should learn [about Git](working-conventions/git) first. For instructions on actually getting a GHC source tree, see [Getting The Sources](building/getting-the-sources). For information on using these repositories (via submodules), see [the Submodules page](working-conventions/git/submodules).


## Repository listing



The GHC source code tracks many related sub-repositories, which are needed for external dependencies during the build, or tools that are included in the build. Not every sub-repository is maintained by us; in fact, the large majority are *not* maintained by GHC HQ (roughly: if the repository is hosted on Github, it is maintained on Github, and patches and issues should go there).



As a result of this, in HEAD, essentially every single upstream repository we track is tracked with a **git submodule**. These submodules are mirrored for us, and we send patches we need to the upstream maintainer. Here are the submodules we use, and where their upstreams point:


<table><tr><th>**Location in tree**</th>
<td> </td>
<th>**Upstream repo**</th>
<td> </td>
<th>**Upstream GHC branch**</th>
<td> </td>
<th>**Installed\[1\]**</th>
<td> </td>
<th>**Req'd to build\[2\]**</th></tr>
<tr><th>utils/hsc2hs</th>
<td>           </td>
<th>https://git.haskell.org/hsc2hs.git</th>
<td> </td>
<th>master</th>
<td> </td>
<th>Yes</th>
<td> </td>
<th>Yes</th></tr>
<tr><th>utils/haddock</th>
<td>          </td>
<th>https://github.com/haskell/haddock</th>
<td> </td>
<th>ghc-head</th>
<td> </td>
<th>Yes</th>
<td> </td>
<th>No</th></tr>
<tr><th>nofib</th>
<td>                  </td>
<th>https://git.haskell.org/nofib.git</th>
<td> </td>
<th>master</th>
<td> </td>
<th>N/A</th>
<td> </td>
<th>N/A</th></tr>
<tr><th>libraries/array</th>
<td>        </td>
<th>https://git.haskell.org/packages/array.git</th>
<td> </td>
<th>master</th>
<td> </td>
<th>Yes</th>
<td> </td>
<th>Yes</th></tr>
<tr><th>libraries/binary</th>
<td>       </td>
<th>https://github.com/kolmodin/binary</th>
<td> </td>
<th>master</th>
<td> </td>
<th>Yes</th>
<td> </td>
<th>Yes</th></tr>
<tr><th>libraries/bytestring</th>
<td>   </td>
<th>https://github.com/haskell/bytestring</th>
<td> </td>
<th>master</th>
<td> </td>
<th>Yes</th>
<td> </td>
<th>Yes</th></tr>
<tr><th>libraries/Cabal</th>
<td>        </td>
<th>https://github.com/haskell/Cabal</th>
<td> </td>
<th>master</th>
<td> </td>
<th>Yes</th>
<td> </td>
<th>Yes</th></tr>
<tr><th>libraries/containers</th>
<td>   </td>
<th>https://github.com/haskell/containers</th>
<td> </td>
<th>master</th>
<td> </td>
<th>Yes</th>
<td> </td>
<th>Yes</th></tr>
<tr><th>libraries/deepseq</th>
<td>      </td>
<th>https://git.haskell.org/packages/deepseq.git</th>
<td> </td>
<th>master</th>
<td> </td>
<th>No</th>
<td> </td>
<th>No</th></tr>
<tr><th>libraries/directory</th>
<td>    </td>
<th>https://git.haskell.org/packages/directory.git</th>
<td> </td>
<th>master</th>
<td> </td>
<th>Yes</th>
<td> </td>
<th>Yes</th></tr>
<tr><th>libraries/filepath</th>
<td>     </td>
<th>https://git.haskell.org/packages/filepath.git</th>
<td> </td>
<th>master</th>
<td> </td>
<th>Yes</th>
<td> </td>
<th>Yes</th></tr>
<tr><th>libraries/haskeline</th>
<td>    </td>
<th>https://github.com/judah/haskeline</th>
<td> </td>
<th>master</th>
<td> </td>
<th>Yes</th>
<td> </td>
<th>Yes</th></tr>
<tr><th>libraries/haskell98</th>
<td>    </td>
<th>https://git.haskell.org/packages/haskell98.git</th>
<td> </td>
<th>master</th>
<td> </td>
<th>Yes</th>
<td> </td>
<th>Yes</th></tr>
<tr><th>libraries/haskell2010</th>
<td>  </td>
<th>https://git.haskell.org/packages/haskell2010.git</th>
<td> </td>
<th>master</th>
<td> </td>
<th>Yes</th>
<td> </td>
<th>Yes</th></tr>
<tr><th>libraries/hoopl</th>
<td>        </td>
<th>https://git.haskell.org/packages/hoopl.git</th>
<td> </td>
<th>master</th>
<td> </td>
<th>Yes</th>
<td> </td>
<th>Yes</th></tr>
<tr><th>libraries/hpc</th>
<td>          </td>
<th>https://git.haskell.org/packages/hpc.git</th>
<td> </td>
<th>master</th>
<td> </td>
<th>Yes</th>
<td> </td>
<th>Yes</th></tr>
<tr><th>libraries/old-locale</th>
<td>   </td>
<th>https://git.haskell.org/packages/old-locale.git</th>
<td> </td>
<th>master</th>
<td> </td>
<th>Yes</th>
<td> </td>
<th>Yes</th></tr>
<tr><th>libraries/old-time</th>
<td>     </td>
<th>https://git.haskell.org/packages/old-time.git</th>
<td> </td>
<th>master</th>
<td> </td>
<th>Yes</th>
<td> </td>
<th>Yes</th></tr>
<tr><th>libraries/process</th>
<td>      </td>
<th>https://git.haskell.org/packages/process.git</th>
<td> </td>
<th>master</th>
<td> </td>
<th>Yes</th>
<td> </td>
<th>Yes</th></tr>
<tr><th>libraries/terminfo</th>
<td>     </td>
<th>https://github.com/judah/terminfo</th>
<td> </td>
<th>master</th>
<td> </td>
<th>Yes</th>
<td> </td>
<th>Yes</th></tr>
<tr><th>libraries/time</th>
<td>         </td>
<th>https://github.com/haskell/time</th>
<td> </td>
<th>master</th>
<td> </td>
<th>Yes</th>
<td> </td>
<th>Yes</th></tr>
<tr><th>libraries/unix</th>
<td>         </td>
<th>https://github.com/haskell/unix</th>
<td> </td>
<th>master</th>
<td> </td>
<th>Yes</th>
<td> </td>
<th>Yes</th></tr>
<tr><th>libraries/Win32</th>
<td>        </td>
<th>https://git.haskell.org/packages/Win32.git</th>
<td> </td>
<th>master</th>
<td> </td>
<th>Yes</th>
<td> </td>
<th>Yes</th></tr>
<tr><th>libraries/xhtml</th>
<td>        </td>
<th>https://github.com/haskell/xhtml</th>
<td> </td>
<th>master</th>
<td> </td>
<th>Yes</th>
<td> </td>
<th>Yes</th></tr>
<tr><th>libraries/random</th>
<td>       </td>
<th>https://github.com/haskell/random</th>
<td> </td>
<th>master</th>
<td> </td>
<th>No</th>
<td> </td>
<th>No</th></tr>
<tr><th>libraries/primitive</th>
<td>    </td>
<th>https://github.com/haskell/primitive</th>
<td> </td>
<th>master</th>
<td> </td>
<th>No</th>
<td> </td>
<th>No</th></tr>
<tr><th>libraries/vector</th>
<td>       </td>
<th>https://github.com/haskell/vector</th>
<td> </td>
<th>master</th>
<td> </td>
<th>No</th>
<td> </td>
<th>No</th></tr>
<tr><th>libraries/dph</th>
<td>          </td>
<th>https://git.haskell.org/packages/dph.git</th>
<td> </td>
<th>master</th>
<td> </td>
<th>No</th>
<td> </td>
<th>No</th></tr>
<tr><th>libraries/parallel</th>
<td>     </td>
<th>https://git.haskell.org/packages/parallel.git</th>
<td> </td>
<th>master</th>
<td> </td>
<th>No</th>
<td> </td>
<th>No</th></tr>
<tr><th>libraries/stm</th>
<td>          </td>
<th>https://git.haskell.org/packages/stm.git</th>
<td> </td>
<th>master</th>
<td> </td>
<th>No</th>
<td> </td>
<th>No</th></tr></table>


- \[1\] These libraries are not installed in the resulting compiler when you do `make install`

- \[2\] These libraries are not required to build the compiler, but may be used for tests or other libraries. Right now, most of these are based on whether you build DPH. At the moment, DPH is turned off. To build these libraries, set `BUILD_DPH=YES` in `mk/build.mk`. You can skip haddock by setting `HADDOCK_DOCS=NO` in `mk/build.mk`. TODO Explain how to skip `deepseq`, since it seems to only be used for tests.
