# The adapter is verified as a component and released with the core

- Status: accepted
- Scope: host
- Date: 2026-07-28

## Context and Problem Statement

The adapter earned a release claim when it learned to find loops. Recording it
in `.host-software` as its own component made its artifact verifiable, which is
worth having: the recipe now proves that binary re-derives in the pinned
toolchain alongside the other two.

Running the release phase against it did the wrong thing, and the way it went
wrong is worth writing down.

The phase materialised a second checkout of the repository, one per component,
and bumped the version in the root manifest it found there. The adapter is a
workspace member rather than the root package, so what it bumped was the core's
version, under a tag naming the adapter. The next version came from the core's
too, which put it three releases ahead of anything the adapter had ever claimed.
The change was discarded before it reached a commit.

This is not a defect in the tool so much as an assumption meeting a shape it did
not expect: one component, one repository, one package. Two crates in one
repository defeats it.

## Decision

The adapter keeps its component stanza and skips the release phase, and the
skip cites this record.

**It keeps the stanza** because that is what makes its artifact verifiable.
`software --verify-build` re-derives all three binaries in the recorded
toolchain, and dropping the adapter to avoid the release phase would trade a
real check for a bookkeeping convenience.

**It skips the release** because the repository has one release cadence.
[call/0011](0011-the-adapter-sits-beside-the-core.md) put both crates in one
repository on purpose, so that the register format the adapter writes and the
core parses cannot drift between them. One repository means one tag, and the
core's tag already covers the adapter's source. A second tag over the same
commits would claim a second release of one thing.

The adapter's own version stays informational: it records what the crate is,
rather than a release anyone can fetch by that name.

## Consequences

- Good: the artifact is verified and reproducible, which is the property worth
  having, and the release bookkeeping stops claiming something untrue.
- Good: the two crates cannot drift, since one tag covers both.
- Bad: an operator wanting the adapter alone has no tag naming it, and takes the
  core's tag and builds the member. That is a real cost and a small one.
- Neutral: were the adapter ever to move to its own repository, the release
  phase would work on it unchanged, and this record would be superseded rather
  than worked around.
