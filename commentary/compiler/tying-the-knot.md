# Tying the knot



Background reading: [
GHC at The Architecture of Open Source Applications](http://www.aosabook.org/en/ghc.html) (search for "No Symbol Table").



Compilers usually have one or more data structures known as *symbol tables*, which are mappings from symbols to information about the symbol in question. GHC avoids symbol tables; instead, a symbol *contains* all information about itself. Thus, the data types for [Haskell entities](commentary/compiler/entity-types) (Id, TyVar, TyCon, DataCon, and Class) form an immutable cyclic data structure, where everything points to everything else. This makes it very convenient for the consumer, because there are accessor functions with simple types, such as `idType :: Id -> Type`.



The downside of these cyclic data structures is that they are difficult to update.  This has two implications: (1) we have to construct this graph in one go using a technique called *tying the knot* (since we can't update the graph after the fact--it's immutable!) and (2) if any of the data in the graph ever becomes out-of-date (as can occur when typechecking hs-boot loops), we have to throw out all of the in-memory data structures and rebuild the graph from scratch.  How this knot tying works is a dark corner of GHC, but hopefully this wiki page will shed some light on the matter.


## Practical advice


- Add a `pprTrace` to a type environment, and have GHC spin into a loop or panic? This may be because you are forcing a thunk too early. Try printing out the unique keys of the environment instead, or moving the trace later.

- Consider using `mkNaked****` instead of the usual functions if you are within the knot-tying code

## Graph representation versus interface representation



Most type-checker entities which form the graph representation have a sister representation which is not cyclic, uses symbol tables, and suitable for serialization into an interface file.  Here are some of the main correspondences:


```wiki
Interface           Graph
---------------------------------------------
data ModIface       data ModDetails
data IfaceDecl      data TyThing
  = IfaceData       data TyCon = AlgTyCon
  | IfaceSynonym               | SynonymTyCon
  | IfaceFamily                | FamilyTyCon
  | IfaceClass      data Class
  | IfaceId         data Id
  | IfaceAxiom      data CoAxiom
  | IfacePatSyn     data PatSyn
data IfaceConDecl   data DataCon
data IfaceType      data Type
data IfaceExpr      data CoreExpr
data IfaceCoercion  data Coercion
data IfaceClsInst   data ClsInst
data IfaceFamInst   data FamInst
data IfaceRule      data CoreRule
```


Taking `IfaceType` and `Type` as an example, we can see the big difference in a constructor for type constructor application:


```
data Type
  = ...
  | TyConApp TyCon [KindOrType]

data IfaceType
  = ...
  | IfaceTyConApp IfaceTyCon IfaceTcArgs
data IfaceTyCon
  = IfaceTyCon { ifaceTyConName :: IfExtName
               , ifaceTyConInfo :: IfaceTyConInfo }       
```


In `Type`, the type constructor application contains the full `TyCon` which contains everything we could possibly want to know about the type constructor (e.g., if it is a synonym, what its unfolding is). In `IfaceType`, the application points to a stub data structure `IfaceTyCon` which only records the `IfExtName` of the `TyCon` in question (which is just a `Name`); to find out more information, we will have to go out and lookup this `IfExtName` in the symbol table associated with the interface type.



**Converting from graph to interface representation.**  The primary functions which turn `ModDetails` into `ModIface` are `mkIface` (when we generated code) and `mkIfaceTc` (when we did not generate code.) There is also `tyThingToIfaceDecl` (which does the obvious thing) and `toIface*****` functions. These functions are all relatively straightforward: since all graph representations record globally unique `Name`s identifying them, all we need to do is drop the extra information and preserve only the `Name`.



**Converting from interface to graph representation.** This process is referred to as *typechecking the interface* (in `TcIface`).  Unlike the conversion to interface format, which is essentially pure, conversion from the interface format involves some global state: the existing graph of type checking entities which we are going to resolve `Name` references to. Generally speaking, there is a \*unique\* such graph, such that every `Name` maps to a unique `TyThing` in the graph, spanning over all of the typechecked entities GHC could possibly know about. In the common case, the only reason we typecheck an interface is to (lazily) load its entities into this global map.


## Tying the knot when loading interfaces



Consider the following Haskell file:


```
data T = MkT S
data S = MkS T
```


When we serialize this into an interface file, we end up with two `IfaceDecl`s: one for `T` and one for `S`, both of which contain names to refer to one another.  But when it comes time to construct the `TyCon` representing these types, we are in trouble: the `TyCon` for `T` has a `DataCon` whose `Type` needs to refer to the `TyCon` for `S`, which in turn refers to the `TyCon` for `T`: we have a cycle. How can we *tie the knot* in order to let these two `TyCon`s refer to each other? The idea is that we lazily defer typechecking the details about the `TyCon` (in particular, its `DataCon`s) until we have populated the type environment for this interface with all of the `TyThing`s (i.e., the `TyCon`) which we may refer to locally.



There are three parts to this:


1. First, `typecheckIface` in `TcIface` typechecks all of the `IfaceDecl`s in the `ModIface`, and then writes them into a mutable variable which makes them available to other typechecking code to tie the knot:

  ```
                  -- Typecheck the decls.  This is done lazily, so that the knot-tying
                  -- within this single module work out right.  In the If monad there is
                  -- no global envt for the current interface; instead, the knot is tied
                  -- through the if_rec_types field of IfGblEnv
          ; names_w_things <- loadDecls ignore_prags (mi_decls iface)
          ; let type_env = mkNameEnv names_w_things
          ; writeMutVar tc_env_var type_env
  ```

1. Second, the actual process of typechecking these declarations is done *lazily* using the `forkM` function (which unsafely converts a monadic typechecking operation into a thunk).

1. Finally, in `tcIfaceGlobal` in `TcIface`, whenever we would try to get the `TyThing` for a `Name` that is locally defined in the interface, we look it up instead in the mutable variable. Because this call to `tcIfaceGlobal` is suspended lazily, it won't get called until the mutable variable is populated with all the things we need.


The net effect is that at the point when we `loadDecls` the declarations, we have a list of `Name`s and unevaluated `TyThing` thunks, which we write into the global environment. Later, when we actually force the `TyThing` thunk, the suspended typechecking computation goes ahead and looks up the thunk in the environment, which has since been updated with the thunks we need.



**Variation: tying the knot when typechecking mutually recursive interfaces.** Sometimes, recursive declarations can be spread out across several `hi` files (due to an `hs-boot` loop). In this case, laziness plays a similar role; however, instead of consulting per-interface mutable variable, the typechecking process consults the External Package State (EPS) (in the case of `ghc -c`) or the [Home Package Table (HPT)](commentary/compiler/driver) (in the case of `ghc --make`).  As before, laziness plays a critical role: we first add thunks representing all of the declarations to the EPS without doing any interface typechecking; then when we force the thunk the names can be found by doing the lookup.



Something extra is needed in the case of `ghc --make`: when we typecheck against an `hs-boot` file, we may build `TyThing`s which refer to incomplete `TyThing`s provided from typechecking the `hs-boot` file. In this case, it's necessary to \*re-typecheck\* all of the interfaces in the module loop, so that they end up pointing to the most up-to-date `TyThing`s.


## Tying the knot when typechecking a module



As we typecheck Haskell source code, we produce `TyCon`s and other type-checking entities for them. If some declarations are mutually recursive, then we need to similarly tie the knot.  There are two primary cases when this can occur:



**A mutually recursive set of source declarations.** GHC simply arranges for every declaration in a mutually recursive set of declarations to be typechecked "all at once." For example, `tcTyClDecls` in `TcTyClsDecls` uses `fixM` to refer to the resulting type declarations, so they can be placed in the environment when we typecheck these very type declarations.



**An hs file which implements an hs-boot file.** This is the trickiest case of knot-tying during type checking, so let's look at a particular example:


```
-- A.hs-boot
module A where
data T
-- B.hs
module B where
import {-# SOURCE #-} A
data S = MkS T
-- A.hs
module A where
import B
data T = MkT S
```


Like before, `T` and `S` form a mutually recursive loop; the difference is this time it is done through an `hs-boot` file.  At the point in time when we typecheck `A.hs`, we would like the `TyCon`s for `T` and `S` to be mutually recursive.



However, this leads to a very intruiging requirement: when we typecheck the interface for `B.hi`, we must tie the knot with the local type environment (while typechecking.) Thus, rather than a mutable variable for the interface, we need to refer to a mutable variable for the current type-checking session.  This variable is `tcg_type_env_var` in `TcGblEnv`. It is updated at various points during the typechecking session, including when we setup the type environment in `tcTyClDecls` (`tcExtendRecEnv` does the dirty work.)



This leads to another complication with `ghc --make`: just how we must retypecheck the interface files after we finish typechecking a module loop, we must also retypecheck the interface files BEFORE we start typechecking, so that the knot-tying can take place. (Actually, hypothetically you could remove the later retypecheck, but we need it so that we can get up-to-date unfoldings, which aren't computed until after we run the optimizer, which is after all the thunks have been forced.)

