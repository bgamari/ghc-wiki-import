# Standard Collection Libraries


## Mission Statement



Provide the Haskell language a reliable, stable, coherent, efficient, function-rich collections library.


1. Reliable: As few bugs as possible. This also implies the next goal, stability.
1. Stable: 

  - The API should change as rarely as possible. API changes must be backward compatible.
  - Changes in the behaviour should be rare as well, and for the generally-agreed better.

1. Coherent: both behaviour and APIs should be coherent across the libraries, in order not to confuse the user.
1. Efficient, feature-rich: Apart from the obvious, this means that more than one implementation will be needed in the long run.

## Related Tickets


- [Create a new bug report](/trac/ghc/trac/ghc/newticket?version=6.4.1&keywords=collections&component=libraries/base&type=bug)
- [Create a new task](/trac/ghc/trac/ghc/newticket?version=6.4.1&keywords=collections&component=libraries/base&type=task)
- [Create a new feature request](/trac/ghc/trac/ghc/newticket?version=6.4.1&keywords=collections&component=libraries/base&type=feature+request)

- View open bug reports (Ticket query: keywords: \~collections,
  component: libraries/base, status: new, status: assigned, status: reopened,
  type: bug, order: priority)
- View tasks (Ticket query: keywords: \~collections, component: libraries/base,
  status: new, status: assigned, status: reopened, type: task, order: priority,
  group: difficulty)
- View feature requests (Ticket query: keywords: \~collections,
  component: libraries/base, status: new, status: assigned, status: reopened,
  type: feature+request, order: priority)

- View all tickets (Ticket query: keywords: \~collections,
  component: libraries/base, status: new, status: assigned, status: reopened,
  order: priority)

## API and policies


### Module-level API



The existing Data.Map and Data.Set define a de-facto API. This API is important because many existing haskell programs use it. (See the corresponding haddock documentation for a "definition" of the API.)



Implementors of alternatives to Data.Map and Data.Set are advised to stick as closely as reasonably possible to the existing API. This would allow users to switch between implementations by just changing their import directive.



However, there are legitimate reasons not to strictly stick to the same interface. Should an implementor choose to do so, minor changes, or not-misleading changes, should be considered first. For example:


- Drop a function
- Change the context (constraints) of a function type
- Harmless changes in behaviour (ie. different asymptotic performance)


A important case, discussed at length in the mailing list, concerns left-biasing in Sets and key-sets of Maps in the presence of non-structural equality. Assuming structural equality (or not enforcing the left-bias) is considered a minor change, because few people actually rely on it in their programs.



Misleading changes are strongly discouraged:


- Changing the type of a function, besides the constraints.
- Reusing a name for a different purpose
- Using a different name for the same purpose

### Class-based API



The definition of a set of classes for collection types is in progress.
See the [CollectionClassFramework](collection-class-framework) for details.


## Work done


- Standardized testing framework. 

  - Checks correctness of the libraries.
  - The same is used independantly of the element types.

## Short terms plans


- Have a consistent performance checker. [\#676](https://gitlab.staging.haskell.org/ghc/ghc/issues/676)


 


## Long term plans & ideas



 


- Gather alternative collection implementations. Those must pass the regression tests. Note that this implies that they conform to the naming conventions already in place, or that compatibility layers are added.
  Candidates for inclusion:

  - Adrian Hey's AVL trees
  - Tries
  - ...
- Use AVL trees implementation as default
- Performance tests should compute the complexity bounds automatically.
