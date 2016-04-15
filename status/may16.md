# GHC Status Report, October 2015



As usual, GHC development churns onward - and **GHC 8.0 is right around the corner**! The final set of bugs are being fixed, and we hope to have a final release candidate, followed by the final release, in just a few weeks.


# Major changes in GHC 8.0.1


- **Support for simple, implicit callstacks with source locations** \[ImplicitCallstacks\] implicit parameters providing callstacks/source locations\], allowing you to have a light-weight means of getting a call-stack in a Haskell application. ([
  Phab:D861](https://phabricator.haskell.org/D861))

- **Injective type families** \[[InjectiveTypeFamilies](injective-type-families)\]. Injective TFs allow you to specify type families which are injective, i.e. have a one-to-one relationship. ([
  Phab:D202](https://phabricator.haskell.org/D202)).

- **Applicative do notation** \[[ApplicativeDo](applicative-do)\]. With the new `-XApplicativeDo`, GHC tries to desugar do-notation to `Applicative` where possible, giving a more convenient sugar for many common Applicative expressions. ([
  Phab:D729](https://phabricator.haskell.org/D729))

- **A beautiful new users guide**. Now rewritten in reStructured Text, and with significantly improved output and documentation.

- **Visible type application** - \[[ExplicitTypeApplication](explicit-type-application)\]. This allows you to say, for example `id @Bool` to specialize `id` to `Bool -> Bool`. With this feature, proxies are never needed.

- **Kind Equalities**, which form the first step to building Dependent Haskell. This feature enables promotion of GADTs to kinds, kind families, heterogeneous equality (kind-indexed GADTs), and `* :: *`. ([
  Phab:D808](https://phabricator.haskell.org/D808))

- **Record system enhancements** \[[OverloadedRecordFields](overloaded-record-fields)\]. At long last, ORF will finally be available in GHC 8.0, allowing multiple uses of the same field name and a form of type-directed name resolution.

- A huge improvement to pattern matching (including much better coverage of GADTs), based on the work of Simon PJ and Georgios Karachalias. For more details, see [
  their paper](http://research.microsoft.com/en-us/um/people/simonpj/papers/pattern-matching/gadtpm.pdf).

- **More Backpack improvements**. There's a new user-facing syntax which allows multiple modules to be defined a single file, and we're hoping to release at least the ability to publish multiple "units" in a single Cabal file. **TODO FIXME**: Edward, is there ANY documentation about this?!

- **Support for DWARF based stacktraces** \[DWARF\]. from Peter Wortmann, Arash Rouhani, and Ben Gamari with backtraces from Haskell code.

# Upcoming post-8.0 plans



Naturally, there were several things we didn't get around to this cycle, or things which are still in flight and being worked on. (And you can always try to join us if you want something done!)


## Libraries, source language, type system


- TypeableT (Ben, Simon, etc)
- Future source-visible Backpack plans? (Edward should answer)
- What about MonadFail? (Herbert, David L)
- FIXME accumulate some of the scattered changes/plans for `base`. (Edward K, Austin, Herbert?)

## Back-end and runtime system


- Compact changes? (Edward Y)
- Maybe mention -fexternal-interpreter here? (Simon M)
- AIX support goes here? (Herbert)
- ARM RTS support is working extremely well now? (Ben, Erik)
- Someone heroically ported GHC to m68k! (Sergei, with link to an awesome blog post)

## Frontend, build system and miscellaneous changes


- New shake build system is Really Finally Going to get merged? (Andrey)
- The ImprovedLLVMBackend plan didn't make the cut for 8.0, but will for 8.2 (Austin)
- Donated Mac buildbot, thanks to FutureIce! (Austin)
- GHC-pkg is learning some new tricks, RE: environment files and Cabal changes. (Austin/Duncan?)

# Development updates and "Thank You"s



Insert incredible levels of praise and adoration for contributors like Thomas, Tamar, Ömer, etc, and
lots of our newer regular contributors like Ryan, Michael Sloan, etc.


# References



Insert many links pointing deeply into the web, so you can read even more.

