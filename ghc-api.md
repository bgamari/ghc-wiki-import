CONVERSION ERROR

Original source:

```trac
= GHC 7.10 GHC-API Changes and proposed changes

There are a number of changes in GHC 7.10 that will make it easier for
tool writers to use the GHC API.

These include

1. More parser entrypoints, to explicitly parse fragments [Andrew Gibiansky]

  The following parsers are now provided

    parseModule, parseImport, parseStatement, ​ parseDeclaration,
    parseExpression, parseTypeSignature, ​ parseFullStmt, parseStmt,
    parseIdentifier, ​ parseType, parseHeader

2. No more landmines in the AST [Alan Zimmerman]

  In the past it was difficult to work with the GHC AST as any generic
  traversals had to carefully tiptoe around an assortment of panic and
  undefined expressions. These have now been removed, allowing
  standard traversal libraries to be used.

3. Introduce an annotation structure to the ParsedSource to record the location of uncaptured keywords [Alan Zimmerman]

  At the moment the location of let / in / if / then / else / do
  etc is not captured in the AST. This makes it difficult to
  parse some source, transform the AST, and then output it again
  preserving the original layout.

  The current proposal, which can be seen at [1] and a proof of
  concept implementation at [2] returns a structure keyed to each AST
  element containing simply the specific SrcSpan's not already
  captured in the AST.

  This is the analogue of the Language.Haskell.Exts.Annotated.Syntax
  from haskell-src-exts, except a custom SrcSpanInfo structure is
  provided for each AST element constructor, and it is not embedded
  within the AST.

  [1] GhcAstAnnotations

  [2] https://phabricator.haskell.org/D297


```