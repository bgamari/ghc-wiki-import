CONVERSION ERROR

Original source:

```trac
[[PageOutline]]

== Nofib results ==

Full results here (updated '''May 5th, 2015''')

'''NB''': The baseline here is 7.6.3

https://gist.githubusercontent.com/thoughtpolice/4fd02766ea0ce7925817/raw/396987c0ea46af9ca29d380e67a35faa6d15a495/gistfile1.txt

'''NB''': Rerun in slow mode for nofib, to get more accurate results.

=== Nofib outliers ===

==== Binary sizes ====

==== Allocations ====

==== Runtime ====

== tests/perf/compiler` results ==

=== 7.6 vs 7.8 ===

  - A bit difficult to decipher, since a lot of the stats/surrounding numbers were totally rewritten due to some Testsuite API overhauls.
  - The results are a mix; there are things like `peak_megabytes_allocated` being bumped up a lot, but a lot of them also had `bytes_allocated` go down as well. This one seems pretty mixed.
  
=== 7.8 vs 7.10 ===

  - Things mostly got **better** according to these, not worse!
  - Many of them had drops in `bytes_allocated`, for example, `T4801`.
  - The average improvement range is something like 1-3%.
  - But one got much worse; `T5837`'s `bytes_allocated` jumped from 45520936 to 115905208, 2.5x worse!

=== 7.10 vs HEAD ===

  - Most results actually got **better**, not worse!
  - Silent superclasses made HEAD drop in several places, some noticeably over 2x
    - `max_bytes_used` increased in some cases, but not much, probably GC wibbles.
  - No major regressions, mostly wibbles.

== Build times ==

(NB: Sporadically updated)

'''As of April 22nd''':

  - GHC HEAD: 14m9s  (via 7.8.3) (because of Joachim's call-arity improvements)
  - GHC 7.10: 15m43s (via 7.8.3)
  - GHC 7.8:  12m54s (via 7.8.3)
  - GHC 7.6:  8m19s  (via 7.4.1)

Random note: GHC 7.10's build system actually disabled DPH (half a dozen more packages and probably a hundred extra modules), yet things *still* got slower over time!

```