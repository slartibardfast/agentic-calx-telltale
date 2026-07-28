# The census adapter sits beside the core, out of the canonical artifact

- Status: accepted
- Scope: software
- Date: 2026-07-28

## Context and Problem Statement

The census adapter reads an image and drafts a register from it. To do that it
has to decode instructions, which means an object-format reader and a
disassembler, and neither is something this project should write.

That collides with a property the core has and did not set out to have. Its
dependency list is empty, which lets the release build offline inside the pinned
container straight from source, where every other component in this host needs a
vendored bundle to manage the same thing. The property fell out of keeping the
proof boundary narrow, and it turned out to be the cheapest reproducible build
here.

Putting the reader in the core would spend that. The offline build would need a
vendored bundle, the bundle would need a hash and a release to host it, and the
thing that makes this component simple to re-derive would be gone. Spending it
for a component most runs never invoke is a poor trade.

Writing the reader instead is worse than it first looks. Parsing an object file
is bounded work. Decoding instructions to find back edges is architecture-
specific, unbounded, and exactly the kind of surface where a defect is a
security problem rather than a wrong answer, since the input is a binary
somebody else built.

## Decision

The adapter is a separate crate in this repository, built separately, and out of
the canonical artifact.

- **Beside the core, not inside it.** The verified arithmetic keeps its empty
  dependency list, its offline build, and its narrow proof boundary. The adapter
  depends on the core rather than the reverse.
- **In the same repository.** The adapter emits the register format and the core
  parses it, so the two have to move together. Separate repositories would let
  the format drift between them, which is the failure this arrangement exists to
  avoid.
- **Out of the canonical artifact.** The released binary stays the core command
  line, built with no dependencies. The adapter is built on request, vendors
  what it needs, and carries its own recipe if it is ever released.

This follows the arrangement the reference host already uses for its own heavy
readers, which are opt-in and kept out of the canonical artifact for the same
kind of reason.

## Consequences

- Good: the property that makes this component's release cheap survives, and it
  survives on purpose rather than by luck.
- Good: the disassembly surface is quarantined. A defect there cannot reach the
  arithmetic, and the arithmetic is what carries the claims.
- Good: the register format has one definition and two users in one repository,
  so it cannot drift.
- Bad: an operator who wants the adapter builds it separately, which is a step
  the single-binary arrangement would not have needed.
- Neutral: whether the adapter is ever released as its own artifact is left
  open. It has no releasable claim until it has one, and its version records
  that rather than disguising it. A number tracking the core would imply a
  lockstep it does not have, and one left behind at an old release would read as
  drift; zero says neither and needs no hand-synchronising when the core is
  released. It is marked unpublishable for the same reason.
