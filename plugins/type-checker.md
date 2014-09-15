CONVERSION ERROR

Original source:

```trac
= Type Checker Plugins =

== Motivation ==

There is much interest at present in various extensions to GHC Haskell
type checking:

 * Type-level natural numbers, with an SMT solver... (Iavor Diatchki)

 * ...or integer ring unification (Christiaan Baaij)

 * Units of measure, with a solver for abelian group unification (Adam Gundry)

 * Type-level sets and maps, e.g. for effect tracking

All of these share a common pattern: they introduce extensions to the
language of constraints or the equational theory of types, and
corresponding extensions to the constraint solving algorithm X that
underlies GHC's type checking algorithm OutsideIn(X).  In principle,
OutsideIn is parametric in the constraint solver, but in practice GHC
provides only one solver, which supports type families and GADT
equality constraints.

Type families can be used to encode some of the desired extensions,
but they do not provide exactly the desired equational theory, and
this leads to worse type inference behaviour and worse error messages
than we might expect for a native implementation.

The aim of this proposal is to make it easier to experiment with
alternative constraint solvers, by making it possible to supply them
in normal Haskell libraries and dynamically loading them at compile
time, rather than requiring implementation inside GHC itself.  This is
much like the situation for [wiki:Plugins Core plugins], which allow
experiments with transformations and optimizations of the intermediate
language.  The fact that plugins can be developed without recompiling
GHC is crucial, as it reduces barriers to entry and allows the
resulting constraint solvers to be used by non-developers.


== Design ==

=== Creating a plugin ===

A type checker plugin, like a Core plugin, consists of a normal Haskell
module that exports an identifier `plugin :: Plugin`.  We extend the
`CoreMonad.Plugin` type with an additional field:

{{{
data Plugin = Plugin
  { installCoreToDos :: ... -- as at present
  , tcPlugin         :: [CommandLineOption] -> Maybe TcPlugin
  }

data TcPlugin = forall t . TcPlugin
  { init  :: TcM t
  , solve :: t -> [Ct] -> [Ct] -> TcS ([SolveResult], [Ct])
  , close :: t -> TcM ()
  }

data SolveResult = Stuck | Impossible | Simplified EvTerm
}}}

The exact design of the `TcPlugin` interface is open to debate, but
the basic idea is as follows:

 * When type checking a module, GHC calls `init` once before constraint
   solving starts.  This allows the plugin to initialise mutable state
   or open a connection to an external process (e.g. an external SMT
   solver).

 * During constraint solving, GHC repeatedly calls `solve`.  Given
   lists of Given and Wanted constraints, this function should attempt
   to simplify the Wanteds, returning a `SolveResult` corresponding to
   each Wanted, and a list of additional constraints to introduce.  If
   the plugin solver makes progress, GHC will re-start the constraint
   solving pipeline, looping until a fixed point is reached.

 * Finally, GHC calls `close` after constraint solving is finished,
   allowing the plugin to dispose of any resources it has allocated
   (e.g. terminating the SMT solver process).

Note that the `TcM` and `TcS` monads can perform arbitrary IO,
although some care must be taken with side effects (particularly in
`TcS`).  In general, it is up to the plugin author to make sure that
any IO they do is safe.  The existentially quantified variable `t`
allows the plugin to initialise some state and pass a handle to the
function that does the solving.


=== Using a plugin ===

Just as at present, a module that uses a plugin must request it with a
new GHC command-line option `-fplugin=<module>` and command line
options may be supplied via `-fplugin-opt=<module>:<args>`.

This means that a user should always know which plugins are affecting
the type checking of a module.  It does mean that a library that relies
on a special constraint domain (e.g. for units of measure), and
exposes types involving these constraints, may need its users to
explicitly activate a plugin for their programs to type check.  This is
probably desirable, since type checker plugins may cause unexpected
type checker behaviour (even performing arbitrary IO).

If multiple type checker plugins are specified, they will be
initialised, executed and closed in the order given on the command
line.  This makes it possible to use plugins that work on disjoint
constraint domains (e.g. a units of measure plugin and a type-level
numbers plugin), or even experiment with combining plugins for the
same constraint domains.


=== Evidence ===

The interface sketched above expects type checker plugins to produce
evidence terms `EvTerm` for constraints they have simplified.
Different plugins may take different approaches generating this
evidence.  The simplest approach is to use "proof by blatant
assertion": essentially this amounts to providing an axiom `forall s t
. s ~ t` and trusting that the constraint solver uses it in a sound
way.  However, in some cases (such as an abelian group unifier used
for units of measure) it should be possible for the solver to encode
the axioms of the equational theory and build proofs from them.


== Open questions and future directions ==

 * At which point should the plugin constraint solver be called during
   GHC's constraint solving process?  Before or after the main
   constraint solver, or does it depend on the plugin?  We may need an
   additional variant on the solver for the simplifying givens stage.

 * Do we want to provide other extension points for the type checker,
   for example extending typeclass or type family instance lookup, or
   changing the generalisation/defaulting behaviour?

 * For units of measure, it would be nice to be able to extend the
   pretty-printer for types (so we could get a nicely formatted
   inferred type like `m/s^2` rather than a nested type family
   application).  This ought to be possible using a plugin approach,
   provided we can thread the required information via the
   `SDocContext`.

 * It would be nice for plugins to be able to manipulate the error
   messages that result from type checking, along the lines of error
   reflection in Idris.

== Reactions from community ==

'''Richard:''' I really like the use of an existential type variable.
 * Should `solve` have the ability to modify the custom state? (I don't know -- just thinking about it.)
 * Should the plugin specify some domain of interest, so that it isn't barraged with irrelevant constraints? (Perhaps not -- it's easy enough for the plugin to do its own filtering.)
 * I think the interface you describe subsumes type family / type class lookup, as both of these take the form of constraints to be solved.
 * I don't like forcing all users of a library to have to specify the plugin on the command line. It's very anti-modular. I ''do'' like requiring all users to opt into using plugins at all, say with `-XCustomConstraintSolvers`, but then the module that needs the custom solver should specify the details. There should also be a mechanism whereby importing (even transitively) a module that needs a custom solver can warn users to enable `-XCustomConstraintSolvers`.

Have you tried out this interface? Does it work?
'''End Richard'''

'''Christiaan''':
The interface looks fine by me, I do have some questions/remarks:
 * How does the solver architecture check/enforce that the SolveResults match up with the Wanted constraints?
   Perhaps return tuples of wanted constraints and correspondig SolveResults?
 * Do we consider a result with a list of all Stuck SolveResults, but with new constraints, as progress?
'''End Christiaan'''

```