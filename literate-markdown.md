# Literate Markdown



Markdown has grown in popularity since github started encouraging people to write their documentation with it.
Github highlights the source according to how it's labeled, so the haskell code blocks look nice, as do the HTML blocks.
As an example, here is a blog post written in markdown and rendered by github's source view:



[
https://github.com/elliottt/elliottt.github.com/blob/source/source/\_posts/2013-02-19-serenade-in-haskell.markdown](https://github.com/elliottt/elliottt.github.com/blob/source/source/_posts/2013-02-19-serenade-in-haskell.markdown)



And here is the source:



[
https://raw.github.com/elliottt/elliottt.github.com/source/source/\_posts/2013-02-19-serenade-in-haskell.markdown](https://raw.github.com/elliottt/elliottt.github.com/source/source/_posts/2013-02-19-serenade-in-haskell.markdown)



As you can see, the source is a haskell module, and if GHC accepted .md or .markdown as input, this blog post would be executable.


## Current Literate Processing



Haskell already supports literate files, using two different styles:



Using "bird-tracks":


```wiki
This is a comment.  Lines starting with '>' are the actual code.

> average xs = sum xs / length xs
```


Or, using the LaTeX compatible notation:


```wiki
This is a comment.

\begin{code}
average = sum xs / length xs
\end{code}
```


Unfortunately, neither of this is compatible with mark-down: in mark-down the bird-tracks signify quoting (just like in e-mail clients)
and, of course, `\begin{code}` is LaTeX. 


## The Proposal



The idea is to extend Haskell's literate notation so that it is compatible with markdown, in the same way that `\begin{code}` makes
it work with LaTeX.  This is great for two reasons:


1. `markdown` is a simple language that is used by many programmers
1. there are many existing tools that know how to process the `markdown` notation (e.g., `github`, `pandoc`, etc.)


To support literate Haskell written in markdown we need two changes:


1. A new way to indicate what are the code parts in a literate Haskell file
1. (Optional, but nice.)  Disable bird-tracks style Haskell blocks in markdown files, so that GHC does not accidentally interpret quotes as code.

### How to Recognize Code Blocks



A new code block would be started by ````` or ````haskell` at the beginning of a line, the code starts on the next line.  The code block
is terminated by a line that starts with `````.  For example:


```wiki
# Heading

This is a comment, and next there will be some code:

```haskell
x :: Int
x  = 10
```
```


Markdown supports snippets of code in different languages, which is why there is `haskell` after the ticks.
If a language is not explicitly specified, then we assume that the block contains Haskell---after all, we are
working with literate Haskell files.  


### When to Use Literate Markdown



Currently, GHC supports mixing various styles code blocks---one may have a literate Haskell
file that contains both bird-tracks and LaTeX style literate Haskell.   It is very unclear if
this generality is ever useful.



For literate markdown we propose a different approach: the style of the literate Haskell file
will be determined by the file's extension.  So, if the compiler is handed a file ending in `.md` or `.markdown`,
the common suffixes for `markdown` files, then it will unlit the file using the markdown convention.
Ordinary `.lhs` files will be processed as usual, so this is fully backward compatible.


## Implementation



The above design has been implemented in [
https://github.com/elliottt/ghc/tree/literate-markdown](https://github.com/elliottt/ghc/tree/literate-markdown) .



The implementation involved the following:


1. Modify `unlit` to support the new form of code block
1. Don't get confused with CPP handline (see bug [\#4836](https://gitlab.staging.haskell.org/ghc/ghc/issues/4836))
1. Make GHC look for modules in `.md` and `.markdown` files.
1. TODO when unliting markdown, do not recognize bird-tracks as code blocks.
