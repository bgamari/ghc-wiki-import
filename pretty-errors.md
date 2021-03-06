# Pretty Error messages in GHC


## Status as of July 2017


- There is some work afoot moving GHC to `prettyprinter`. See [\#14005](https://gitlab.staging.haskell.org/ghc/ghc/issues/14005).

## Status as of June 2017



The following was extracted from Ben's response to Siddharth Bhat on [
ghc-devs](https://mail.haskell.org/pipermail/ghc-devs/2017-June/014280.html) in June 2017,


>
>
> I saw this ticket on trac: [\#8809](https://gitlab.staging.haskell.org/ghc/ghc/issues/8809).
> I would like to take this up, but I'd like help / pointers and stuff. I
> have GHC setup, I know how to use phabricator, but.. where do I start? :)
>
>


This ticket has recently seen quite a bit of activity and I've been
meaning to write down some thoughts on it. Here it goes:



Currently Alfredo Di Napoli is [
working](https://github.com/haskell/pretty/pull/43) on the `pretty` library to
both improve performance and allow us to drop GHC's fork (see [\#10735](https://gitlab.staging.haskell.org/ghc/ghc/issues/10735)),
perhaps to use annotated pretty-printer documents. Meanwhile, David
Luposchainsky, has recently [
released](https://www.reddit.com/r/haskell/comments/6e62i5/ann_prettyprinter_10_ending_the_wadlerleijen_zoo/) his `prettyprinter` library
which may serve as a drop-in replacement to `pretty` and handles all of
the cases that Alfredo is working on. Moreover, Shivansh Rai has also
recently expressed interest in helping out with this effort.



All of this is great news: I have been hoping we'd get Idris-style
errors for quite some time. However, given how many hands we have in
this area, we should be careful not to step on each toes. Below I'll
describe the various facets of the task as I see them.



There is also a github repo intended to catalogue instances of bad error messages, [
here](https://github.com/bollu/hask-error-messages-catalog)


## Choice of pretty printer



It seems like we first need to resolve the question of whether switching
from (our fork of) `pretty` to the `prettyprinter` library is
worthwhile. The argument for moving to `prettyprinter` is its support
for optimized infinite-band-width layout, which is one of the things
holding us back from moving back to `pretty`.



However, there are two impediments to switching,


- `prettyprinter` depends upon the `text` package while GHC does not.
  Making GHC dependent on `text` is an option, but we should be
  careful. Adding a dependency has a non-trivial cost (GHC build times
  rise, GHC API users are stuck using whatever dependency versions GHC
  uses, release engineering is a bit more complicated).

>
> >
> >
> > Currently GHC has its own abstractions for working with text
> > efficiently, LitString and FastString. FastString is used throughout
> > the compiler, including the pretty-printer, and represents a
> > dense UTF-8 buffer (and a hash for quick comparison). It's not clear that we
> > would want to move it to `text` as this would introduce UTF-8/UTF-16
> > conversion.
> >
> >
>

- `prettyprinter` doesn't support rendering to `String`. This
  essentially means that we either use `Text` or fork the package.
  However, if we have already decided on depending on `text`, then
  perhaps the former isn't so bad.


It's unclear to me exactly how difficult switching would be compared to
finishing up the work Alfredo has started on `pretty`. Alfredo, what is
your opinion?



If we decide against moving to `prettyprinter`, then we will need to
finish up something like Alfredo's `pretty` patches to rid GHC of its
fork.


## Representing rich error messages in GHC



In my opinion we should avoid baking more stylistic decisions (e.g. printing
types in red, terms in blue) into the modules like TcErrors which produce
error messages. This is why I propose that we use annotated
pretty-printer documents in [\#8809](https://gitlab.staging.haskell.org/ghc/ghc/issues/8809) (see comment 3). This would allow us
to represent the typical things seen in GHC error messages (e.g. types,
terms, spans, binders, etc.) in structured form, allowing the error
message consumer (e.g. GHC itself, a GHC API user, or some JSON error
sink) to make decisions about how to present these elements to the user.



I think this approach give us a much better story for dealing with the
problems currently solved by flags like -fprint-runtime-reps,
-fprint-explicit-kinds, etc., especially for users using an IDE.



As far as I can recall, there was still a bit of disagreement
surrounding whether the values carried by the error message should be
statically or dynamically typed. In particular, Richard Eisenberg
advocated that error message documents look like,


```
-- A dynamically typed value embedded in an error message
data ErrItem = forall a. (Outputable a, Typeable a). ErrItem a

type ErrDoc = Doc ErrItem
```


Whereas I argue that this would quickly become unmaintainable,
especially when one considers GHC API users. Rather, I say that we
should encode the "vocabulary" of things that may appear in an error
message explicitly,


```
data ErrItem = ErrType Type
             | ErrSpan Span
             | ErrTerm HsExpr
             | ErrInstance ClsInst
             | ErrVar  Var
             | ...
```


While there are good arguments for both options, although I think that
in balance an explicit approach will be better for consumers. Anyways,
this is a question that will need to be answered.



Once there is consensus I think it shouldn't be too difficult to move
things forward. The change can be made incrementally and for the most
part should only touch a few modules (with the bulk in TcErrors).


### What do we represent?



There is also the question of what the vocabulary of embeddable items
should consist of. I think the above are pretty non-controversial but I
can think of a variety of items which would more precisely capture
some common patterns,


```
data ErrItem = ...
             | ErrExpectedActual Type Type
               -- ^ e.g. "Expected type: ty1, Actual type: ty2"
             | ErrContext Type
               -- ^ Like ErrType but specifically captures a context
             | ErrPotentialInstances [ClsInst]
               -- ^ A list of potentially matching instances
             | ...
```


Exactly how far we want to go is something that would need to be
decided. I think we would want to start with the minimal set initially
proposed and then introduce additional items as we gain experience with
the scheme.


## Using rich error messages



Once we have GHC producing rich error documents we can teach GHC's
command line driver to prettify them. We can also teach haskell-mode,
ghc-mod, and friends to preserve their structure to give the user an
Idris-like experience.



Exactly how many stylistic decisions we want GHC to make is a tricky
question; this is prime territory for bike-shedding and people tend to
have rather strong aesthetic beliefs; keeping things simple while
satisfying all tastes may be a challenge.


## Summary



Above I discussed several tasks and a few questions,


- We need to decide on whether David's `prettyprinter` library is right
  for GHC; having a prototype patch introducing it to the tree would
  help in evaluating this. Alfredo, what is your opinion here?

- If not we need to drop our fork of `pretty` in favor of upstream

- We need consensus on whether Idris-style annotated pretty-printer
  documents are the right approach for GHC (I think we are close to
  this)

- If we want annotated documents, should the items be statically or
  dynamically typed?

- Once these questions are resolved we can start introducing
  annotations into GHC's error documents (this shouldn't be hard)

- Then we can teach GHC and associated tooling to pretty-print these
  rich messages prettily


There is certainly a fair bit of work here although it's not
obvious how to parallelize it across all of the interested
parties. Regardless, I would be happy to advise on any bit of this.


