# GHC Status



This page summarises the state of play on GHC


## Releases



Here are our [release](working-conventions/releases) plans for


- [GHC 8.8.1](status/gh-c-8.8.1) (next major release)


We release GHC on multiple platforms; the [platforms page](platforms) gives details.



For fun: the release plans for previous releases:


- [GHC 8.6.3](status/gh-c-8.6.3) (current major release)
- [GHC 8.6.2](status/gh-c-8.6.2)
- [GHC 8.6.1](status/gh-c-8.6.1)
- [GHC 8.4.4](status/gh-c-8.4.4)
- [GHC 8.4.3](status/gh-c-8.4.3)
- [GHC 8.4.2](status/gh-c-8.4.2)
- [GHC 8.4.1](status/gh-c-8.4.1)
- [GHC 8.2.2](status/gh-c-8.2.2)
- [GHC 8.2.1](status/gh-c-8.2.1)
- [GHC 8.0.2](status/gh-c-8.0.2)
- [GHC 8.0.1](status/gh-c-8.0.1)
- [GHC 7.10.3](status/gh-c-7.10.3)
- [GHC 7.10.2](status/gh-c-7.10.2)
- [GHC 7.10.1](status/gh-c-7.10.1)
- [GHC 7.8.4](status/gh-c-7.8.4)
- [GHC 7.8.3](status/gh-c-7.8.3)
- [GHC 7.8.1](status/gh-c-7.8)
- [GHC 6.12](status/gh-c-6.12)
- [GHC 6.10](status/gh-c-6.10)

## Automated builds and performance testing



We keep notes on compiler performance


- [Performance/Runtime](performance/runtime) for issues pertaining to the performance of code generated by GHC.
- [Performance/Compiler](performance/compiler) for issues pertaining to the performance of GHC itself.


We have several automated ways of monitoring GHC.  Each has its own detailed description page.


- [Harbormaster](phabricator/harbormaster) is a part of [Phabricator](phabricator), which builds all [
  GHC commits](https://phabricator.haskell.org/diffusion/GHC/history/) and incoming patches for testing.
- [The GHC builders](builder-summary) build GHC every night on multiple platforms.
- [Travis](travis) also watches the repository for new commits (any branch) and validates them. [](https://travis-ci.org/ghc/ghc/builds)
- [
  Our performance dashboard](http://perf.haskell.org/ghc) monitors changes in the performance of GHC itself, and of programs compiled by GHC, with a per-commit granularity.
- [ Haskell.org server status page](http://status.haskell.org/)

## Components



Template Haskell has its own status page at [TemplateHaskell/Status](template-haskell/status).


## Tickets and patches


- [Summary of open tickets](status/tickets), listed by component.
- [Ticket statistics](status/ticket-stats): the number of tickets created in a certain year, and their current status.
- [Status/SLPJ-Tickets](status/slp-j--tickets) is a curation of interesting tickets by SPJ
- The [GHC bug sweep](bug-sweep) attends to lost and forgotten tickets.

- [
  A list of all Phabricator patches](https://phabricator.haskell.org/differential/query/dUJ4ndtfSChZ/)
- [
  A list of all Phabricator patches that have been accepted](https://phabricator.haskell.org/differential/query/5LIb9B9n_08b/)


GHC's Trac is also used by the [
Haskell Core Libraries Committee](http://www.haskell.org/haskellwiki/Core_Libraries_Committee) to track progress on changes to the [
core libraries](http://www.haskell.org/haskellwiki/Library_submissions#The_Core_Libraries):


- [
  Active Core Libraries tickets](https://ghc.haskell.org/trac/ghc/query?status=infoneeded&status=merge&status=new&status=patch&status=upstream&component=Core+Libraries&col=id&col=summary&col=component&col=status&col=type&col=priority&col=milestone&order=priority) (these tickets have "Component" set to "Core Libraries")

## Biannual status reports



Here are biannual GHC status reports, published in the [
Haskell Communities and Activities Report](http://haskell.org/communities/)


- [GHC status October 2018](status/oct18)
- [GHC status April 2018](status/apr18)
- [GHC status October 2017](status/oct17)
- [GHC status April 2017](status/apr17)
- [GHC status October 2016](status/oct16)
- [GHC status May 2016](status/may16)
- [GHC status October 2015](status/oct15)
- [GHC status May 2015](status/may15)
- [GHC status October 2014](status/oct14)
- [GHC status May 2014](status/may14)
- [GHC status October 2013](status/oct13)
- [GHC status May 2013](status/may13)
- [GHC status October 2012](status/oct12)
- [GHC status May 2012](status/may12)
- [GHC status October 2011](status/oct11)
- [GHC status May 2011](status/may11)
- [GHC status October 2010](status/oct10)
- [GHC status April 2010](status/apr10)
- [GHC status October 2009](status/oct09)
- [GHC status May 2009](status/may09)
- [GHC status October 2008](status/october08)
- [GHC status May 2008](status/may08)
- [GHC status November 2007](status/nov07)
- [GHC status April 2007](status/april07)
- [GHC status October 2006](status/october06)
