# The founding design note stays outside this repository

- Status: accepted; the non-disclosure constraint lifted by operator ruling, 2026-07-28
- Scope: host
- Date: 2026-07-27

## Context and Problem Statement

calx-telltale was specified by a design note written alongside a firmware
investigation. The note carries the data model, the seven proof obligations, the
composition semantics, the phasing, and the acceptance criteria. Every milestone
in `plan/` derives from it.

The note also carries material this repository must not hold. Its symbol and
suite names are pseudonyms for a codebase under non-disclosure, its worked
figures come from a real image, and it states on its own first page that it is
kept outside the repository on purpose, as a design note about tooling rather
than a project artefact.

A public repository that reproduced it would republish those figures under a
thin renaming, and would do so on the strength of an inference about how much
the pseudonyms conceal.

## Decision

The note is not committed here, and it is not committed to the software
repository either.

What crosses into this host is the design and nothing else: the data model, the
obligations, the operator semantics, the milestone ordering, and the stated
exclusions. Those are restated in this host's own words under `plan/` and in the
software README, with no worked figure traceable to the investigation, and no
suite name whether real or pseudonymous.

Acceptance criteria that depend on figures from the investigation are held with
the note rather than here. A milestone's acceptance criteria in `plan/` are
therefore stated as properties (a short-circuit reports its expensive branch, a
wrapping measure fails termination) rather than as expected numbers.

If the note ever moves into a repository, it needs review against the
non-disclosure terms first, and the operator makes that call.

**That call has been made.** The operator records that the note was already
heavily redacted before it reached this work, and that it has not been committed
anywhere. The caution this record imposed on figures is therefore lifted, and a
later session need not treat the note's worked numbers as untouchable.

The acceptance criteria stay stated as properties even so, for the reason the
next section gives rather than for the one that prompted it: a property holds
across images and a figure holds for one. Nothing here needs rewriting to use
numbers it was avoiding.

## Consequences

- Good: this host and the software repository are publishable as they stand.
- Good: the acceptance criteria are stated as properties, which is the more
  durable form, and they do not depend on one image.
- Bad: the milestones cite a document a reader cannot open. This record exists
  so that absence is deliberate and legible rather than an oversight.
- Neutral: a fixture suite reproducing the note's worked figures can be added
  later in a private component, and would be declared in `.host-software` like
  any other.
