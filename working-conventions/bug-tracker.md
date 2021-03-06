


# The Bug Tracker



We organise our work (both bug fixing and feature requests) using the Trac bug tracker.   There are links to the bug tracker in the sidebar under "View tickets" and "Create ticket". See also:


- [the bug reporting guidelines](report-a-bug)
- [How to fix a bug in GHC](working-conventions/fixing-bugs)

## Type and status



Every ticket has a **status** and a **type**, which appear in the title of the ticket.  Thus "Ticket [\#2057](https://gitlab.staging.haskell.org/ghc/ghc/issues/2057) (new bug)" means status=new, and type=bug.  Here's what they mean:


- **Type** is one of `bug`, `feature request` or `task`.

- **Status** says what state the ticket is in.  It is one of these:

  - **New** is for open tickets that need to be triaged or fixed.
  - **Infoneeded** means that the ticket is stalled awaiting information from the submitter.
  - **Closed** means what it says.
  - **Merge** means that a fix has been committed to the HEAD, but should be propagated to the current release branch.
  - **Patch** means that the ticket includes a patch for review.  We love patches!  So we try hard to review patches promptly and either commit them, or start a conversation with the author.
  - **Upstream** means the ticket includes a patch, BUT the patch is required for an upstream library, and GHC will need to synchronize those changes via `git submodule` (see [WorkingConventions/Git](working-conventions/git).)


The intention is that tickets do not live in the Merge or Patch state for long.



You change the status of a ticket using the Action box at the bottom.  Particularly, if you add a patch for a ticket, click the "please review" action at the bottom to change its status to "Patch".


### Responsibilities



For any given status, 'someone' is responsible for what happens to it next:


- **New** means the bug is still open, and thus in the hands of the GHC team.
- **Infoneeded** means that the ticket is stalled awaiting information from the submitter.
- **Merge** tickets will be handled by Austin or Herbert and merged into the tree before the next release.
- **Patch** tickets will be handled by Austin or Herbert and merged into the tree.
- **Upstream** means that we're waiting on action from an upstream maintainer. Normally, when a ticket is marked 'upstream', a change will be proposed to the maintainer, who will merge it (or something equivalent). The ticket will then be moved back to **patch** status, indicating GHC developers need to pick up the change.

## Other Trac ticket fields



Each ticket has a bunch of other fields too:


- **Milestone**: this field is for the GHC development team to indicate by when we intend to fix the bug.  We have a milestone for each planned release (e.g. "6.12.3"), and three special milestones:

  - An empty milestone field means the bug has not been triaged yet.  We don't yet know if the
    ticket is a real, unique, issue.  Once this has been established, the ticket will be given
    a milestone.
  - **Research needed** is for tickets where the issue can be considered a bug but the solution is an open research problem.
  - **\_\|\_** is for tickets that have been triaged, but we don't plan to fix them for a particular
    release.  This might be because the bug is low priority, or is simply too hard to fix right now.

- **Priority**: this field is for the GHC development team to help us prioritise what we work on. On a release milestone, the highest priority tickets are blockers for that release, and the high priority tickets are those that we also plan to fix before releasing. We will also try to fix as many of the normal and lower priority tickets as possible.

- **Test Case**: fill in this field with the name of the test in the test suite.  Typically every bug
  closed should have an appropriate test case added to the test suite.

  Typically we name the regression the same as the ticket (eg "T7901" for [\#7901](https://gitlab.staging.haskell.org/ghc/ghc/issues/7901)), but the test case field:

  - is a quick check that there IS a regression test
  - works even if the test is named differently (eg `ParserNoForallUnicode` in the case of [\#7901](https://gitlab.staging.haskell.org/ghc/ghc/issues/7901))
  - works if there are multiple tests
  - tells which directory to look in (eg `parser/should_fail` in the case of [\#7901](https://gitlab.staging.haskell.org/ghc/ghc/issues/7901))

- **cc**: we pay more attention to tickets with a long cc list, so add yourself to the cc list if you care about the ticket.  It is *vastly* more effective if you also add a comment to explain why you care. 

- **Owned by** says who is committed to taking the ticket forward.  We typically leave this field blank until we actively start working on it, lest others feel unable to work on a ticket because it is apparently owned, even though nothing is happening.

## Re-milestoning tickets after a release



When a release is made, any open tickets on that release's milestone will be moved to the next release's milestone.



However, when moving onto a milestone for a later major release, the priority is dropped one level (or moved to `_|_` if it is already lowest) - unless one of these is true:


- priority \> normal
- contains a patch for review
- significant support in the CC field


For example, let us follow a ticket about a bug in 7.0.x that doesn't get fixed.


- Created in a 7.2.x milestone, priority normal
- When 7.2 branch is closed, moved to a 7.4.x milestone, priority low
- When 7.4 branch is closed, moved to a 7.6.x milestone, priority lowest
- When 7.6 branch is closed, moved to a `_|_` milestone, priority normal


If it were initially filed in a 7.0.x milestone then it would remain priority normal when moved to 7.2.x.


## Workflow



The ticket workflow is illustrated in the following image. Most tickets will start in state "new" and, once fixed, possibly go via state "merge" if they are suitable for merging to the stable branch, before moving to state "closed". They may also go via state "infoneeded" if more information is needed from the submitter, or "patch" if a patch that needs review has been attached to the ticket.



The following diagram is generated directly based on Trac's current configuration:



Enable JavaScript to display the workflow graph.



## Convenient search queries


- All `merge` tickets: [
  https://ghc.haskell.org/trac/ghc/query?status=merge&order=priority](https://ghc.haskell.org/trac/ghc/query?status=merge&order=priority)
- All `patch` tickets (also available on the sidebar): [
  https://ghc.haskell.org/trac/ghc/query?status=patch&order=priority](https://ghc.haskell.org/trac/ghc/query?status=patch&order=priority)
- All `upstream` tickets: [
  https://ghc.haskell.org/trac/ghc/query?status=upstream&order=priority](https://ghc.haskell.org/trac/ghc/query?status=upstream&order=priority)
