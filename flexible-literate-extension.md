# Flexible Literate Haskell File Extensions



Literate haskell files currently contain two (or more?) types on content, but the current `.lhs` extension doesn't offer tools any insight what the non-haskell content of a file is. This is problem when, for example, using Pandoc to convert literate haskell files into other documents. The goal of this proposal is to give other tools the information they need to figure out what the non-haskell content of a file is. This issue was raised two times on the mailing list ([
https://www.haskell.org/pipermail/glasgow-haskell-users/2014-March/024745.html](https://www.haskell.org/pipermail/glasgow-haskell-users/2014-March/024745.html) and [
https://www.haskell.org/pipermail/glasgow-haskell-users/2014-August/025228.html](https://www.haskell.org/pipermail/glasgow-haskell-users/2014-August/025228.html) respectively) and the corresponding Trac ticket [9789](https://gitlab.staging.haskell.org/ghc/ghc/issues/9789).



This page aims to summarise the results of the previous discussion and clarify the impact of the proposal, as requested by thomie.


## Current Situation



Currently GHC finds source files by computing a list of valid search paths, converting the module name to relative path, base filename and set of extensions. For every path in the set of search paths, GHC tries the relative path + base filename combination for all of the possible extensions (currently 4). GHC then caches the result for any found module.



GHC will try the following extensions, in order, and picks the first match: `hs`, `lhs`, `hsig` and `lhsig`.


## Proposal



The proposal is to encode the type of the non-haskell content in a literate haskell file in the extension. This would involve generalising the allowed extension for literate haskell files to include a wildcard that indicates the type of the non-haskell content in the file. During the earlier mailing list discussion, the following naming conventions were proposed:


- `Foo.md.lhs`
- `Foo.lhs.md`
- `Foo.md+lhs`
- `Foo.lhs+md`
- `Foo+md.lhs`


Here the markdown `md` extension is placeholder that could be replaced with any extension.


### Alternative



One alternative proposed on the mailing list was to include a textual fingerprint in the file that could be used to identify the content. However, this is not compatible/portable across different content types. Additionally this would require tools to open and inspect files to be able to determine what the content is.


## Concerns



Some concerns raised during the earlier discussion:



The original proposal's `Foo.md.lhs` suggestion works well with GHC, but is not very portable to other compilers. While GHC maps the module name `Foo.Bar.Baz` to the path `Foo/Bar/Baz.hs`, other compilers, however, use different schemes, for example JHC can map the `Foo.Bar.Baz` module name to the file `Foo.Bar.Baz.hs`. This would not be a problem since haskell does not allow lowercase module names, so `Foo.md.lhs` would not be a valid module. Unfortunately GHC is also used on platforms that have a case-insensitive filesystem, where this approach breaks.



Another concern for the various `+` variants was whether this is legal on windows. The Microsoft documentation ([
http://msdn.microsoft.com/en-us/library/windows/desktop/aa365247(v=vs.85).aspx](http://msdn.microsoft.com/en-us/library/windows/desktop/aa365247(v=vs.85).aspx)) does not specify `+` as a restricted character and testing shows these are legal on Windows.



The question how this change interacts with other compilers and the Haskell Report was raised. The haskell report (perhaps unwisely) does not specify the mapping of module names to filenames, and is therefore a non-issue for this change. Given the lack of standardisation there is no way to rely on portability between compilers in existing haskell anyway. The proposal tries to avoid conflicts with other compilers were possible and there, currently, are no known conflicts between this change and existing compilers.



The last raised concern was how this change would impact boot files, I would keep the hs-boot/lhs-boot extension for boot files, as literate boot files do not seem particularly valuable, although I'm open to comments on this.


## Trade-offs



The `Foo.md.lhs` naming scheme is nice, but conflicts with JHC (and potentially other compilers)



The `Foo.lhs.md` avoids conflicts with any other compilers, because files ending in `md` can (currently) never be valid haskell files. `.` separated filenames are easy to deal with in existing filepath libraries. Additionally, it transparently works with existing `md` tools, such as GitHub markdown highlighting.



The `Foo+md.lhs` avoids conflicts with other compilers, but it's hard to find the +md part using existing filepath code, as these mostly support splitting on `.`, as such it is inconvenient to work with in 3rd party tools.



The `Foo.md+lhs` and `Foo.lhs+md` variants are also easy to deal with using standard filepath libraries. The extension being different from both `.lhs` and `.md` avoids the file accidentally being interpreted as the wrong filetype. i.e., `Foo.lhs.md` being accidentally interpreted as a `md`.



Personally I consider `Foo.md.lhs` disqualified due to incompatibility with JHC. `Foo+md.lhs` qualified due to being awkward to work with and ugly looking.



I'm ok with both `Foo.md+lhs` and `Foo.lhs+md`, but currently my personal preference leans towards `Foo.lhs.md`.


## Concrete (Current) Proposal



This proposal liberalises GHC's current module-to-filepath mapping. This means that if, during a module lookup, GHC can't find a file name ending with one of the `hs`, `lhs`, `hsig` or `lhsig` extensions it will perform a scan of the directory and accept any file matching the `Foo.lhs` followed by any extension, such as: `Foo.lhs.md`, `Foo.lhs.rst` or `Foo.lhs.tex`. This proposal does **not** propose any changes to the way the contents of literate haskell files are interpreted/unlit'ed by GHC. While I'm sympathetic to more flexible literate files, I consider those orthogonal proposals.



The boot files extension for this newly allowed extensions would simply remain `Foo.lhs-boot`, as I don't think there's any real value to having literate bootfiles of the `Foo.lhs-boot.md` variety.


