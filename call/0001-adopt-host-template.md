# Adopt the host template at c8fc28c

- Status: accepted
- Scope: host
- Date: 2026-07-27

## Context and Problem Statement

calx-telltale began as a design note and a set of proof obligations with no
repository. The work needs a governed home before any code is written: an
operating manual, the numbered registers, a Where room holding the software
under development, and a route to upgrade as the methodology moves.

The target directory was empty, and its name was `agentic-calx-telltale`.
`host-lifecycle classify` reported case (a): there was no `CLAUDE.md` present
and so nothing to merge.

## Decision

Adopt `https://github.com/connollydavid/host-template` at revision
`c8fc28cd6f7062944a2f046003fa13135a0fc039`, recorded in the `.host` stamp and
registered as the `host-template` submodule at that same commit.

- The template's `CLAUDE.md` is copied unchanged. Conventions specific to this
  project are recorded under a project-specifics heading rather than by editing
  the spine.
- The verification tools are pinned to the commits the template references at
  this revision: host-lint `bb16c46`, host-lifecycle `8b68284`, allium
  `2b7d66f`, specula `6bb5857`. Three are wired as submodules under `tools/`.
- host-lint is carried as a Where-room component instead of a submodule. It has
  to appear in `.host-software` regardless, because that is where the `hooks`
  and `artifact` for the gating tool are recorded, and declaring it in both
  places gave two sources for one skill: `bootstrap` linked
  `.claude/skills/host-lint` from whichever it reached first and then refused
  the other, so the skill link never settled. The reference host carries
  host-lint the same way, as a component and not a submodule.
- The three ledger entries pending at adoption (`RETIRE-hermetic-exempt`,
  `RENAME-repro-waiver`, `REFS-a-number-resolves`) were each recorded through
  their own `verify` post-condition, which a fresh host satisfies without
  action, and the baseline was then advanced to `REFS-a-number-resolves`.

Both repositories are held under the `slartibardfast` account: this host at
`agentic-calx-telltale`, and the software under development at `calx-telltale`.
This follows the arrangement already used for calx-mill.

One spine document was corrected on copy. The template's `STRUCTURE.md` cites
`call/0039` for the bare-store layout, which is a record in the reference host
rather than one an adopter receives, so `refs --check` reads it as a dead
pointer. The citation is dropped here and the statement it annotated is kept.
`CLAUDE.md` already holds that a reference host's own rooms bind no adopter, so
this is that rule applied rather than a departure from the spine.

Three phrases were sanctioned in `LEXICON` through `host-lint lexicon add`, each
one the spine's own vocabulary in either the proof-tool or the agent-tool sense.
`LEXICON` carries the list, so it is not restated here.

## Consequences

- Good: a later case (c) upgrade diffs from an exact revision, and the ledger
  position is unambiguous.
- Good: the tool pins match the template's own, so this host and the reference
  host verify against the same binaries. The gating binary was rebuilt here in
  the recorded muslrust toolchain from the pinned vendored bundle, and it
  reproduces the recorded artifact hash `2451a2a27774` byte for byte, so that
  hash is a claim this host has checked rather than one it copied.
- Neutral: the tools are referenced as submodules rather than vendored, so a
  fresh clone needs `host-lifecycle bootstrap` before the gates run.
