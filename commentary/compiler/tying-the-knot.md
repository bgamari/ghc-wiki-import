# Tying the knot



Background reading: [
GHC at The Architecture of Open Source Applications](http://www.aosabook.org/en/ghc.html) (search for "No Symbol Table").



Compilers usually have one or more data structures known as *symbol tables*, which are mappings from symbols to information about the symbol in question. GHC avoids symbol tables; instead, a symbol *contains* all information about itself. Thus, the data types for [Haskell entities](commentary/compiler/entity-types) (Id, TyVar, TyCon, DataCon, and Class) form an immutable cyclic data structure, where everything points to everything else. This makes it very convenient for the consumer, because there are accessor functions with simple types, such as `idType :: Id -> Type`.



The downside of these cyclic data structures is that they are difficult to update.  This has two implications: 


1. we have to construct this graph in one go using a technique called *tying the knot* (since we can't update the graph after the fact--it's immutable!) and 

1. if any of the data in the graph ever becomes out-of-date (as can occur when typechecking hs-boot loops), we have to throw out all of the in-memory data structures and rebuild the graph from scratch.  


How this knot tying works is a dark corner of GHC, but hopefully this wiki page will shed some light on the matter.


## Status



Knot-tying is all intimately tied up with the treatment of `hs-boot` files, so those tickets are listed here.



Use Keyword = `hs-boot` to ensure that a ticket ends up on these lists.



**Open Tickets:**

<table><tr><th>[\#1012](https://gitlab.staging.haskell.org/ghc/ghc/issues/1012)</th>
<td>ghc panic with mutually recursive modules and template haskell</td></tr>
<tr><th>[\#8441](https://gitlab.staging.haskell.org/ghc/ghc/issues/8441)</th>
<td>Allow family instances in an hs-boot file</td></tr>
<tr><th>[\#9450](https://gitlab.staging.haskell.org/ghc/ghc/issues/9450)</th>
<td>GHC instantiates Data instances before checking hs-boot files</td></tr>
<tr><th>[\#9562](https://gitlab.staging.haskell.org/ghc/ghc/issues/9562)</th>
<td>Type families + hs-boot files = unsafeCoerce</td></tr>
<tr><th>[\#10333](https://gitlab.staging.haskell.org/ghc/ghc/issues/10333)</th>
<td>hs-boot modification doesn't induce recompilation</td></tr>
<tr><th>[\#12034](https://gitlab.staging.haskell.org/ghc/ghc/issues/12034)</th>
<td>Template Haskell + hs-boot = Not in scope during type checking, but it passed the renamer</td></tr>
<tr><th>[\#12063](https://gitlab.staging.haskell.org/ghc/ghc/issues/12063)</th>
<td>Knot-tying failure when type-synonym refers to non-existent data</td></tr>
<tr><th>[\#13069](https://gitlab.staging.haskell.org/ghc/ghc/issues/13069)</th>
<td>hs-boot files permit default methods in type class (but don't typecheck them)</td></tr>
<tr><th>[\#13180](https://gitlab.staging.haskell.org/ghc/ghc/issues/13180)</th>
<td>Confusing error when hs-boot abstract data implemented using synonym</td></tr>
<tr><th>[\#13299](https://gitlab.staging.haskell.org/ghc/ghc/issues/13299)</th>
<td>Typecheck multiple modules at the same time</td></tr>
<tr><th>[\#13322](https://gitlab.staging.haskell.org/ghc/ghc/issues/13322)</th>
<td>Pattern synonyms in hs-boot files</td></tr>
<tr><th>[\#13347](https://gitlab.staging.haskell.org/ghc/ghc/issues/13347)</th>
<td>Abstract classes in hs-boot should not be treated as injective</td></tr>
<tr><th>[\#13981](https://gitlab.staging.haskell.org/ghc/ghc/issues/13981)</th>
<td>Family instance consistency checks happens too early when hs-boot defined type occurs on LHS</td></tr>
<tr><th>[\#14092](https://gitlab.staging.haskell.org/ghc/ghc/issues/14092)</th>
<td>hs-boot unfolding visibility not consistent between --make and -c</td></tr>
<tr><th>[\#14103](https://gitlab.staging.haskell.org/ghc/ghc/issues/14103)</th>
<td>Retypechecking the loop in --make mode is super-linear when there are many .hs-boot modules</td></tr>
<tr><th>[\#14382](https://gitlab.staging.haskell.org/ghc/ghc/issues/14382)</th>
<td>The 'impossible' happened whilst installing gi-gtk via cabal</td></tr>
<tr><th>[\#16127](https://gitlab.staging.haskell.org/ghc/ghc/issues/16127)</th>
<td>Panic: piResultTys1 in compiler/types/Type.hs:1022:5</td></tr></table>




**Closed Tickets:**

<table><tr><th>[\#2412](https://gitlab.staging.haskell.org/ghc/ghc/issues/2412)</th>
<td>Interaction between type synonyms and .hs-boot causes panic "tcIfaceGlobal (local): not found"</td></tr>
<tr><th>[\#4003](https://gitlab.staging.haskell.org/ghc/ghc/issues/4003)</th>
<td>tcIfaceGlobal panic building HEAD with 6.12.2</td></tr>
<tr><th>[\#7672](https://gitlab.staging.haskell.org/ghc/ghc/issues/7672)</th>
<td>boot file entities are sometimes invisible and are not (semantically) unified with corresponding entities in implementing module</td></tr>
<tr><th>[\#10083](https://gitlab.staging.haskell.org/ghc/ghc/issues/10083)</th>
<td>ghc: panic! (the 'impossible' happened)</td></tr>
<tr><th>[\#11062](https://gitlab.staging.haskell.org/ghc/ghc/issues/11062)</th>
<td>Type families + hs-boot files = panic (type family consistency check too early)</td></tr>
<tr><th>[\#12035](https://gitlab.staging.haskell.org/ghc/ghc/issues/12035)</th>
<td>hs-boot knot tying insufficient for ghc --make</td></tr>
<tr><th>[\#12042](https://gitlab.staging.haskell.org/ghc/ghc/issues/12042)</th>
<td>Infinite loop with type synonyms and hs-boot</td></tr>
<tr><th>[\#12064](https://gitlab.staging.haskell.org/ghc/ghc/issues/12064)</th>
<td>tcIfaceGlobal error with existentially quantified types</td></tr>
<tr><th>[\#13140](https://gitlab.staging.haskell.org/ghc/ghc/issues/13140)</th>
<td>Handle subtyping relation for roles in Backpack</td></tr>
<tr><th>[\#13591](https://gitlab.staging.haskell.org/ghc/ghc/issues/13591)</th>
<td>"\*\*\* Exception: expectJust showModule" in ghci with hs-boot</td></tr>
<tr><th>[\#13710](https://gitlab.staging.haskell.org/ghc/ghc/issues/13710)</th>
<td>panic with boot and -jX</td></tr>
<tr><th>[\#13803](https://gitlab.staging.haskell.org/ghc/ghc/issues/13803)</th>
<td>Panic while forcing the thunk for TyThing IsFile (regression)</td></tr>
<tr><th>[\#14075](https://gitlab.staging.haskell.org/ghc/ghc/issues/14075)</th>
<td>GHC panic with parallel make</td></tr>
<tr><th>[\#14080](https://gitlab.staging.haskell.org/ghc/ghc/issues/14080)</th>
<td>GHC panic while forcing the thunk for TyThing IsFile (regression)</td></tr>
<tr><th>[\#14396](https://gitlab.staging.haskell.org/ghc/ghc/issues/14396)</th>
<td>Hs-boot woes during family instance consistency checks</td></tr>
<tr><th>[\#14531](https://gitlab.staging.haskell.org/ghc/ghc/issues/14531)</th>
<td>tcIfaceGlobal (local): not found</td></tr></table>



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



**Variation: tying the knot when typechecking mutually recursive interfaces.** Sometimes, recursive declarations can be spread out across several `hi` files (due to an `hs-boot` loop). In this case, laziness plays a similar role; however, instead of consulting per-interface mutable variable, the typechecking process consults the External Package State (EPS) (in the case of `ghc -c`) or the [Home Package Table (HPT)](commentary/compiler/driver) (in the case of `ghc --make`).



The **external package state** is a mutable variable which stores a mapping from `Name`s to `TyThing`s for all the externally loaded symbols we know about (in the case of `ghc -c`, it also stores symbols from the home package as well). Whenever you call `loadInterface`, this variable is updated with the new symbols from that interface file.  To tie the knot over multiple interface file, laziness plays a critical role: we first add thunks representing all of the declarations to the EPS without doing any interface typechecking for both interfaces; then when we force the thunk the names can be found by consulting the EPS.



The **home package table** is a mapping containing all of the `ModDetails` of modules from the home package which we are currently compiling. It's kept separate from the `EPS` because we often need to remove entities from this environment (e.g., we are in a GHCi session and we update a module.)  Because these `ModDetails` are preserved from compilation to compilation (unlike `ghc -c`, where the EPS is reconstructed from scratch every invocation of GHC), we may have some `ModDetail`s in the HPT which are linked against incomplete `TyThing`s provided from typechecking an `hs-boot` file. Once we compile the real `hs` file for the `hs-boot` file, it's necessary to \*re-typecheck\* all of the interfaces in the module loop, so that they end up pointing to the most up-to-date `TyThing`s; this is done by simply throwing out all of the old `ModDetails` from the HPT and then reloading them back in. (`retypecheckLoop` in `GhcMake`).


## Tying the knot when typechecking a module



As we typecheck Haskell source code, we produce `TyCon`s and other type-checking entities. If some declarations are mutually recursive, then we need to similarly tie the knot.  There are two primary cases when this can occur:



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



However, this leads to a very intriguing requirement: when we typecheck the interface for `B.hi`, we must tie the knot with the local type environment (while typechecking.) Thus, rather than a mutable variable for the interface, we need to refer to a mutable variable for the current type-checking session.  This variable is `tcg_type_env_var` in `TcGblEnv`. It is updated at various points during the typechecking session, including when we setup the type environment in `tcTyClDecls` (`tcExtendRecEnv` does the dirty work.)



This leads to another complication with `ghc --make`: just how we must retypecheck the interface files after we finish typechecking a module loop, we must also retypecheck the interface files BEFORE we start typechecking, so that the knot-tying can take place. Failure to do this lead to [\#12035](https://gitlab.staging.haskell.org/ghc/ghc/issues/12035). (Actually, hypothetically you could remove the later retypecheck, but we need it so that we can get up-to-date unfoldings, which aren't computed until after we run the optimizer, which is after all the thunks have been forced.)


## All of the bits and bobs



GHC's present knot-tying story is a bit hard to understand.  It consists of at least the folowing components (if this seems like a random jumble, it's because it is!):


- `if_rec_types :: Maybe (Module, IfG TypeEnv)` in `IfGblEnv`.  This field affects how typechecking interfaces works. Operationally, when this variable is `Just (mod, get_tyenv)`,  any `Name` from `mod` is typechecked by looking it up in a mutable variable that is accessible via `get_tyenv`.  The source of this mutable variable depends on how the interface monad is run (`initIfaceCheck`, `initIfaceTcRn`, `initIfaceTc`).
- `initIfaceCheck :: HscEnv -> IfG a -> IO a`.  This initializes the interface monad with "no useful info at all."  According to its name, this is only supposed to be used when we are checking if an old interface is up-to-date.  It fills `if_rec_types` depending on if `hsc_type_env_var` is set.  In practice, there are a few uses:

  - `checkOldIface`: We are in the recompilation manager and are trying to decide if an interface is up-to-date.  Lives in `IfG` because we invoke `loadInterface` to get the hashes which we are going to compare against.
  - `genModDetails`: We got an up-to-date interface (from `checkOldIface!`) and now we want to typecheck it into a `ModDetails` so it can be put in the HPT in make mode.  (The HomeModInfo is ignored in one-shot mode.)
  - `typecheckLoop`: In make mode, we finished typechecking an `hs` file which completes a loop: now we need to retypecheck the loop of interfaces so that the HPT properly points to the  right place
  - A few miscellaneous calls `getLinkDeps` and `abiHash` which just want to get the `ModIface` via `loadInterface`.
- `initIfaceTcRn :: IfG a -> TcRn a`.  This initializes the interface monad while typechecking.  In this case, `if_rec_types` is filled using `tcg_type_env_var`, which is updated in the course of typechecking.  This is the preferred way to load things into the EPS because it will knot-tie correctly with the ongoing typechecking computation.
- `tcg_type_env_var` in `TcGblEnv`, which is used to initialize `if_rec_types` when you call `initIfaceTcRn`.  It is updated as we typecheck declarations.  It itself is initialized by `hsc_type_env_var` (in the case of one-shot mode; in make mode any recursive references must be in the HPT already), or just a fresh variable otherwise.
- `hsc_type_env_var` in `HscEnv`, which is used to initialize `tcg_type_env_var` in `initTcRn` and `if_rec_types` in `initIfaceCheck`.  It is set in exactly one place, `hscIncrementalCompile`.  We need to setup the type variable here because `checkOldIface` can load up home modules (this is not the case for make mode; those interfaces are already in the HPT), and we need the "right" type variable to already be fed in when we construct the thunks.
