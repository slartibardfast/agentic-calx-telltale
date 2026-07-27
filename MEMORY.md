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
  the citation on copy; any host adopting this template revision will hit the same thing. Reported as
  [connollydavid/host#19](https://github.com/connollydavid/host/issues/19). Rewriting it as a link does
  not help: the checker matches the literal `call/NNNN` token inside link text and inside the URL path
  alike, so dropping it is the only local fix. The same sweep run against the template itself reports
  eleven dead pointers, ten of them in `UPGRADING.md`, which does not reach adopters.
- `calx-telltale` is greenfield, so its `.host-software` stanza carries no `build` and no `artifact`.
  Those get added, with a pinned toolchain and a recorded hash, by the first milestone producing a binary.

### 2026-07-27 — upgrade to 32e655f

- Case (c) upgrade the same day as adoption: the template moved four commits, one ledger entry pending.
  Baseline is now `GATE-refs-in-verify`, nothing pending. Recorded in call/0005.
- `host-lifecycle refs --gate .` is now a clause of the `verify` phase `recheck`, so a dead pointer
  re-opens the verify receipt and stops a release at its first step. **The gate reads tracked documents
  only**; `refs --check` sweeps the whole working tree including drafts. They answer different questions,
  so do not swap one for the other when diagnosing.
- The entry carries an ordering hazard worth remembering: the `verify` recheck lives in
  `lifecycle.manifest`, which the tool reads live out of the template submodule, so **the gate turns on
  when the pointer moves, not when the entry is recorded**. Bump the pinned host-lifecycle to v0.46.0
  first. This host was already at `8b68284`, which is v0.46.0, so the hazard did not bite. Confirmed by
  probing `host-lifecycle capability refs-gate` rather than assuming.
- Upstream took the STRUCTURE.md fix reported at adoption, in template commit `79c0b71`, citing
  [connollydavid/host#19](https://github.com/connollydavid/host/issues/19). The local edit and the
  upstream one are byte-identical, so re-applying the spine produced no change to that file. The issue
  stays open for the wider question, since a cross-project register citation still has no accepted form.
- `.host-lintignore` is project-owned and was deliberately not taken from the template. The template's
  version excludes its own `UPGRADING.md`; this host has no root `UPGRADING.md` for that to name.
- Re-applying the spine is safe here because `CLAUDE.md` is exactly template-plus-appended-section: the
  spine portion diffs clean against the adopted revision, so it can be regenerated and the
  project-specifics re-appended. **Keep it that way** rather than editing the spine in place.
