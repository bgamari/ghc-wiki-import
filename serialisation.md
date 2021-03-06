CONVERSION ERROR

Original source:

```trac
= Serialisation in Haskell =

This page collects together the various approaches under
discussion for serialising Haskell values, when communicating
between distributed Haskell processes.

The four main approaches are:

 1. Serialisation support in the RTS.
 2. Serialisation as a library.
 3. Serialisation using template Haskell.
 4. Serialisation with a GHC extension for static values.

The following implementations exist.

== Jost Berthold (i.e. Mr Eden) ==

Jost Berthold has done work on serialisation in the Eden RTS

 * RTS support & user API in [http://www.diku.dk/~berthold/papers/mainIFL10-withCopyright.pdf his IFL 2010 paper]

 * Jost's [http://www.haskell.org/wikiupload/2/28/HIW2013PackingAPI.pdf HIW 2013 slides on his API design ideas]

== Jost Berthold: packman ==

Jost has also implemented serialisation as a [https://github.com/jberthold/packman library called packman].

Simon PJ asks: is the Haddock'd documentation available anywhere?

== Cloud Haskell ==

CloudHaskell implements closures with Template Haskell. Closures are
monomorphic in the first implementation. Edsko de Vries made
significant changes, and the hackage document claims it supports static
polymorphic values: [http://hackage.haskell.org/package/distributed-static distributed-static library].

Closures are not first class, which makes the template haskell boilerplate code makes code difficult to read for serialising recursive functions. I've documented this, see Figure 2 in 
[http://www.macs.hw.ac.uk/~hwloidl/MScProjects/FirstClass-HdpH-Serialisation.pdf Rob Stewart's MsC project]

== HdpH ==

HdpH also uses Template Haskell for closure creation. The differences in
closure representation between HdpH and CloudHaskell is described in
section 3.2 of [http://www.dcs.gla.ac.uk/~pmaier/papers/Maier_Trinder_IFL2011_XT.pdf Patrick Maier's IFL'11 paper]

== Mathieu Boespflug at TweagIO ==

Mathieu and Facundo Domínguez have implemented a GHC extension called StaticPointers.

Here's the [https://github.com/tweag/ghc/commit/105929e0280f20f2a0f153e380c40cdb2bd9c79c user manual]

Here are [https://github.com/tweag/ghc/pull/1 all the commits] (to be merged with GHC?):

Mathieu is going to be talking about his serialisation approach on
Saturday at HIW. At least Jost, Patrick and myself will be there.



```
