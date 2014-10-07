## Things to do after releases are made


- Update default GHC version for new Trac tickets. 

- Update [Commentary/Libraries/VersionHistory](commentary/libraries/version-history).

- Delete `__GLASGOW_HASKELL__` ifdefs for versions of ghc older than the [previous 2 major releases](building/preparation/tools). But not from the testsuite, since those ifdefs might exist to be able to compile the test w/ older GHCs in order to compare behavior. Something like (add versions which you want to keep the ifdefs for, remove versions you want to delete them for):

  ```
  git grep -l GLASGOW_ | grep -v 'users_guide\|testsuite' | xargs grep GLASGOW_ | grep -v '\712\|711\|710\|709\|708'
  ```

- Make sure any new Haskell platform release [
  http://www.haskell.org/platform/changelog.html](http://www.haskell.org/platform/changelog.html) corresponds with ghc-pkg list.