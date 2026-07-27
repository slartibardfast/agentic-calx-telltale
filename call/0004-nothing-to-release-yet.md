# Neither component is released by this host

- Status: accepted
- Scope: host
- Date: 2026-07-27

## Context and Problem Statement

The `release` phase is conditional on a Where room and recurring per component,
so it wants a receipt for each of the two components this host records. Neither
can carry a `done` receipt today, and the phase refuses a bare `skip` without a
decision to cite.

## Decision

Both `release` receipts are recorded as `skip`, citing this record.

**calx-telltale** is greenfield. The design is settled and no implementation
exists, so there is no binary to tag, no artifact hash to re-derive, and nothing
a version number would describe. Its `.host-software` stanza carries no `build`
and no `artifact` for the same reason. The first milestone that produces a
binary records the artifact, pins a toolchain, and takes this phase to `done`.

**host-lint** is a consumed tool rather than software under development here. It
is released by its own repository, which runs its own gates and cuts its own
tags. This host pins it, rebuilds it in the recorded toolchain, and checks the
artifact hash. A release cut from here would be a second and competing claim
about a binary this project does not own.

## Consequences

- Good: the skip is a recorded position rather than an unexplained gap, and the
  gate keeps asking until calx-telltale has something to release.
- Neutral: the two components skip for unrelated reasons, and only the
  calx-telltale one is expected to change.
- Bad: a reader sweeping receipts sees two skips against a phase that is
  mandatory in the abstract, and has to come here to learn that one of them is
  permanent.
