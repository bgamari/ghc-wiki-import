CONVERSION ERROR

Original source:

```trac
= Anonymous Records with lenses =

This page is to discuss adding support for Nikita Volkov's record design to GHC.

Links

* The record package on Hackage: http://hackage.haskell.org/package/record
* Homepage and tutorial: http://nikita-volkov.github.io/record/
* Talk: http://www.techcast.com/events/bigtechday8/maffei-1005/
* Mailing list: https://mail.haskell.org/pipermail/ghc-devs/2015-January/008049.html
* Reddit discussion: http://www.reddit.com/r/haskell/comments/2svayz/i_think_ive_nailed_it_ive_solved_the_records/
* More Reddit discussion: https://www.reddit.com/r/haskell/comments/3ck7gv/anonymous_records_in_haskell_nikita_volkov_on_his/
* [wiki:Records/OverloadedRecordFields/Redesign#Designextension:anonymousrecords Notes on the interaction with OverloadedRecordFields]

== Drawbacks ==

The most important drawbacks of this design relative to other designs are:

 * Support of only a limited subset of Haskell syntax (presumably not an issue if built in to GHC)
 * Lack of support for strict and unpacked fields (a recent version provides entirely strict records, but not per-field strictness control otherwise the number of datatypes explodes)
 * Lack of support for polymorphic (Rank-N) fields
 * Fixed limit on the number of fields (24 in the current implementation, but the implementation can easily be updated to extend this range to an arbitrary amount)

Also, it is questionable whether any record extension should bake in a particular lens type.

```
