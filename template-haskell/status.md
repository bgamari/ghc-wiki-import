CONVERSION ERROR

Original source:

```trac
= Current Status of Template Haskell =

This page tracks bug reports and other issues affecting Template Haskell. It is intended to be up-to-date. If, as you're reading this, you see something that's no longer true, '''please change it'''. (We can always look through the history to get old stuff back.)

[https://www.cis.upenn.edu/~eir Richard Eisenberg] is serving as the TH czar as of June 2015.

== Active TH redesigns ==

This section of the page is for links to other wiki pages discussing design changes to Template Haskell. Please put new pages in `TemplateHaskell/Design/YourNewFeature`.

'''Old TH redesigns:''' See TemplateHaskell.

== TH tickets suitable for newcomers ==

New to hacking on GHC? Try one of these:

[[TicketQuery(status=new|infoneeded,keywords=~newcomer,owner=,component=Template Haskell)]]

== Patched TH tickets: ==

These are presumably waiting for review.

[[TicketQuery(status=patch,component=Template Haskell,format=table,col=differential|milestone|summary|priority|owner,order=priority)]]

== Open TH tickets: ==

If you know of a ticket not listed here, please set its "Category" to be "Template Haskell" and it should show up.

[[TicketQuery(status=new,component=Template Haskell,format=table,col=milestone|summary|priority|owner,order=priority)]]
```