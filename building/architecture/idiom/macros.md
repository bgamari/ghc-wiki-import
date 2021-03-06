# Idiom: macros



The build system makes extensive use of Gnu **make** **macros**.  A macro is defined in
GNU **make** using `define`, e.g.


```wiki
define build-package
# $1 = dir
# $2 = distdir
# $3 = GHC stage to use (0 == bootstrapping compiler)
... makefile code to build a package ...
endef
```


(for example, see `rules/build-package`), and is invoked like this:


```wiki
$(eval $(call build-package,libraries/base,dist-boot,0))
```


(this invocation would be in `libraries/base/ghc.mk`).



Note that `eval` works like this: its argument is expanded as normal,
and then the result is interpreted by **make** as makefile code.  This
means the body of the `define` gets expanded *twice*.  Typically
this means we need to use `$$` instead of `$` everywhere in the body of
`define`.



Now, the `build-package` macro may need to define **local variables**.
There is no support for local variables in macros, but we can define
variables which are guaranteed to not clash with other variables by
preceding their names with a string that is unique to this macro call.
A convenient unique string to use is *directory*\_*distdir*\_; this is unique as long as we only call each macro with a given directory/build pair once.  Most macros in
the GHC build system take the directory and build as the first two
arguments for exactly this reason.  For example, here's an excerpt
from the `build-prog` macro:


```wiki
define build-prog
# $1 = dir
# $2 = distdir
# $3 = GHC stage to use (0 == bootstrapping compiler)

$1_$2_INPLACE = $$(INPLACE_BIN)/$$($1_$2_PROG)
...
```


So if `build-prog` is called with `utils/hsc2hs` and `dist` for the
first two arguments, after expansion **make** would see this:


```wiki
utils/hsc2hs_dist_INPLACE = $(INPLACE_BIN)/$(utils/hsc2hs_dist_PROG)
```


The idiom of `$$($1_$2_VAR)` is very common throughout the build
system - get used to reading it!  Note that the only time we use a
single `$` in the body of `define` is to refer to the parameters `$1`,
`$2`, and so on.


