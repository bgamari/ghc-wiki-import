## Examples


### Hello World



To test that everything is working properly, create the following two files:


```wiki
-- hello.bkp
executable hello where
  includes: base
  main-is: hello

-- hello.hs
main = putStrLn "Hello world!"
```


and then compile with:


```wiki
ghc --backpack hello.bkp
./hello
-- Hello world!
```

### Reusable import lists



Suppose you have a Haskell file with an import `import System.Directory (doesFileExist)`; you're using an explicit import list because that's good style, or you don't want to accidentally use a new function that's not available on all versions of the package you care about.  You now want to reuse this import list in another module, but you don't want to copy it over. Here's an example of how to do this in Backpack:


```wiki
package p where
    includes: base
    exposed-modules: P Q
    required-signatures: System.Directory

executable Main where
    includes: base, directory, p
    main-is: Main

-- p/System/Directory.hsig
module System.Directory where
doesFileExist :: FilePath -> IO Bool

-- p/P.hs (the module in question)
module P(ptest) where
import System.Directory
ptest = doesFileExist "no_it_does_not" >>= print

-- p/Q.hs (the second module)
module Q(qtest) where
import System.Directory
qtest = doesFileExist "no_really" >>= print

-- Main.hs (a little test script for the module)
import P
import Q
main = ptest >> qtest
```


These files will successfully compile with `ghc --backpack importlist.bkp`.



You can check that other functions from `System.Directory` are not available by editing `P.hs` or `Q.hs` to attempt to use another function from the module, e.g. `doesDirectoryExist`.



In general, to import a subset of the interface of a module, you create an hsig file which contains the signatures you want and make sure that the original module is not in scope (notice how directory is not included in package p). Then, when you actually want to compile the module (as in the executable Main in this example) make sure you import all the packages that define the modules you defined in this way. 



**Under construction:** The error message you get when you attempt to use a function which is available from the underlying implementation but not from your signature could be improved.

