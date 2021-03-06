CONVERSION ERROR

Original source:

```trac


= Idiom: variable names =

Now that our build system is one giant `Makefile`, all our variables
share the same namespace.  Where previously we might have had a
variable that contained a list of the Haskell source files called
`HS_SRCS`, now we have one of these for each directory (and indeed each build, or distdir) in the source tree,
so we have to give them all different names.

The idiom that we use for distinguishing variable names is to prepend
the directory name and the distdir to the variable.  So for example the list of
Haskell sources in the directory `utils/hsc2hs` would be in the
variable `utils/hsc2hs_dist_HS_SRCS` ('''make''' doesn't mind slashes in variable
names).  The pattern is: ''directory''_''distdir''_''variable''.

See also [wiki:Building/Architecture/Idiom/Macros Idiom: macros] where many applications of this naming idiom are necessary.

== Variables affecting compilation
The file [[GhcFile(rules/distdir-way-opts.mk)]] contains a list of the variables affecting compilation, such as `$1_$2_HC_OPTS` and `$1_$2_MORE_HC_OPTS`.
```
