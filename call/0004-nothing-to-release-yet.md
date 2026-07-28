# Neither component is released by this host

- Status: accepted; the calx-telltale skip is discharged, see below
- Scope: host
- Date: 2026-07-27

## Context and Problem Statement

The `release` phase is conditional on a Where room and recurring per component,
so it wants a receipt for each of the two components this host records. Neither
can carry a `done` receipt today, and the phase refuses a bare `skip` without a
decision to cite.

## Decision

Both `release` receipts are recorded as `skip`, and each cites this record.

**calx-telltale** was greenfield when this was written. The design was settled,
no implementation existed, so there was no binary to tag, no artifact hash to
re-derive, and nothing a version number would describe.

**That is discharged.** The register format and the command line produced a
binary, so the recipe now carries a pinned toolchain, a build, and an artifact
hash, and the release phase records `v0.2.0` rather than a skip. The condition
this record set for itself was the first milestone producing a binary, and that
is exactly what lifted it.

One thing fell out that is worth keeping. The crate has no dependencies, so the
offline build inside the pinned container needed no vendored bundle at all,
which is a reproducibility cost the other components here do pay.

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
