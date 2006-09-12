# GHC Commentary: The C code generator



Source: [compiler/cmm/PprC.hs](/trac/ghc/browser/ghc/compiler/cmm/PprC.hs)



This phase takes [Cmm](commentary/compiler/cmm-type) and generates plain C code.  The C code generator is very simple these days, in fact it can almost be considered pretty-printing.



There are some slight subtleties:


- [info tables](commentary/rts/heap-objects#info-tables), which are expressed in Cmm as being laid out before the entry code for a
  closure, are compiled into separate top-level structures in the generated C, because C has no support for laying out data
  next to functions.  The desired layout is reconstructed in the assembly file by the [Evil Mangler](commentary/evil-mangler),
  or not if we're compiling unregisterised (see [TABLES\_NEXT\_TO\_CODE](commentary/rts/heap-objects#)).
