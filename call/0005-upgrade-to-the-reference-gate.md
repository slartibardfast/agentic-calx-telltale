# Upgrade to 32e655f: the reference sweep joins the verify gate

- Status: accepted
- Scope: host
- Date: 2026-07-27

## Context and Problem Statement

The template moved four commits past the revision this host adopted the same
day. One ledger entry became pending, `GATE-refs-in-verify`, which adds
`host-lifecycle refs --gate .` as a clause of the `verify` phase's `recheck`. A
dead register pointer now re-opens the verify receipt and stops a component
release at its first step.

The entry carries an ordering hazard. The `verify` recheck lives in
`lifecycle.manifest`, which the tool reads live out of the template submodule,
so the gate turns on the moment the pointer moves rather than when the entry is
recorded. An adopter whose pinned host-lifecycle predates v0.46.0 would move the
pointer and then find `software --check` invoking a mode its binary lacks.

## Decision

Upgrade to `32e655f113d908f2a0c43cef8dcf0efaaefc2739`, baseline
`GATE-refs-in-verify`, nothing pending.

The ordering hazard did not apply here. This host already pins host-lifecycle at
`8b68284`, which is v0.46.0, so the required capability was present before the
pointer moved. That was confirmed against the binary rather than assumed.

Spine documents were re-applied across the span and the project-specifics left
alone: `CLAUDE.md` gained the three paragraphs describing the gate and the
deliberate asymmetry between its clauses, and `lifecycle.manifest` gained the
new `recheck`. `STRUCTURE.md` needed no edit, because the upstream fix and the
local one made at adoption are the same change.

`.host-lintignore` was not taken from the template. The template now ships one
that excludes `UPGRADING.md`, which is the template's own record layer. This
host has no `UPGRADING.md` at its root, so the exclusion would name nothing.
Each project owns this file, and ours excludes `MEMORY.md` as before.

## Consequences

- Good: a dead pointer now gates rather than merely reporting, so the reference
  discipline is enforced where it is claimed.
- Good: the local remedy recorded at adoption is now the upstream one, so this
  host carries no divergence from the spine on that line.
- Neutral: the gate reads tracked documents only, so a draft in the working tree
  cannot redden it. `refs --check` remains the way to sweep drafts, and the two
  answer different questions.
- Bad: a cross-project register citation still has no form the sweep accepts, so
  the only ways to write one are to drop it or to quote it as an example. The
  entry names that limitation and leaves it open.
