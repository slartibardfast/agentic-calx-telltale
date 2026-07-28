# The repository carries one version, and the change class is judged over all of it

- Status: accepted
- Scope: software
- Date: 2026-07-28

## Context and Problem Statement

[call/0013](0013-the-adapter-rides-the-core-release.md) settled that one
repository has one release cadence, and that the core's tag covers the adapter's
source. It left a question open that the next release ran straight into: what
does the number on that tag name?

The delta since `v0.4.0` shows why the question has teeth. The adapter gained
`loops`, which is the capability the census existed for. The core gained a help
text that names five verbs it already answered to, and a test that holds it to
that. Every verified module is unchanged.

Read as the core package's version, that is a patch, and the adapter's new
command reaches a reader under a number that says nothing happened. Read as the
repository's version, it is an addition, and the core carries a minor bump that
its own library did not earn.

The release tool sharpens it rather than softens it. It takes a change class and
derives the number, so an author never picks a semver level by hand. That is the
right shape, and it means the class has to be a true statement about something.
The open question is what.

## Decision

**The repository carries one version, and both crates hold it.** The core and
the adapter are bumped together and always read the same. Two numbers moving
independently would be a second cadence, which is the thing `call/0013` refused.

**The change class is judged over the repository's whole surface.** One tag
covers both crates, so the question the class answers is what this tag adds for
someone who takes the release, rather than what one package added. On this
release the answer is a command, so the class is honest.

The version reaches the adapter's manifest by hand until the tool bumps a
workspace member of its own accord. That is the same shape as the trap
`call/0013` recorded: the release phase bumps the root manifest, and a workspace
member is not the root.

## Consequences

- Good: the number means one checkable thing, and the class stays a true
  statement rather than a lever pulled to reach a number someone wanted.
- Good: an operator reads one version for the repository and does not have to
  hold two in mind, which is the simplicity this was asked for.
- Bad: the core's version can move without its library moving, so the number
  does not answer "did the arithmetic change?" A reader wanting that compares
  tags, and the release notes say which crate the change landed in.
- Bad: the adapter's version is set by hand, so it can drift from the core's
  until a release re-aligns it. `software --check` does not catch that today.
- Neutral: were the crates ever to split into separate repositories, each would
  take its own cadence and number, and this record would be superseded along
  with `call/0013`.
