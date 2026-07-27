# MEMORY.md

## Session Log

- Project purpose: A verified calculator for waits, interrupts-off windows, and the compositions built out of them.

### 2026-07-27 — adoption

- Adopted `connollydavid/host-template` at `c8fc28cd6f7062944a2f046003fa13135a0fc039`, case (a): the
  target directory was empty and named `agentic-calx-telltale`, so `host-lifecycle adopt` took route two
  and adopted in place. Recorded in call/0001.
- Both repositories live under the `slartibardfast` account: this host at `agentic-calx-telltale`, the
  software at `calx-telltale`. Matches the arrangement already used for calx-mill.
- Tools pinned to the commits the template references at that revision: host-lint `bb16c46`,
  host-lifecycle `8b68284`, allium `2b7d66f`, specula `6bb5857`.
- Three ledger entries were pending at adoption (`RETIRE-hermetic-exempt`, `RENAME-repro-waiver`,
  `REFS-a-number-resolves`). A fresh host satisfies each `verify` post-condition without action, so each
  was recorded through it and the baseline advanced to `REFS-a-number-resolves`.
- The founding design note is held outside both repositories. Its worked figures come from an image under
  non-disclosure, and it says on its own first page that it stays out. Only the design crossed into the
  host: the data model, the seven obligations, the operator semantics, the milestone order, the
  exclusions. Acceptance criteria in `plan/` are therefore stated as properties rather than as expected
  numbers. Recorded in call/0002. **If a future session is asked to add fixtures reproducing those
  figures, that needs an operator ruling first, not a judgement call.**
- The template's `STRUCTURE.md` cites `call/0039` for the bare-store layout, which is a record in the
  reference host and not one an adopter receives. `refs --check` gates on it as a dead pointer. Dropped
  the citation on copy; any host adopting this template revision will hit the same thing.
- `calx-telltale` is greenfield, so its `.host-software` stanza carries no `build` and no `artifact`.
  Those get added, with a pinned toolchain and a recorded hash, by the first milestone producing a binary.
