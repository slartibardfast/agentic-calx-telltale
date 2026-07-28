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

### 2026-07-28 — the verified core, and a width reversal

- Built the `Quantity` core in calx-telltale: intervals carrying a unit and a provenance, exact rational
  clock rates, the per-register base, the tree of derived clocks, and the delay forms. 32 tests, 12 Kani
  harnesses, all green. Pinned at `41794e6`.
- **Timing sources are declared, never built in** (call/0006). The crate contains no PIT or HPET; those
  appear only as test fixtures. That is what makes it usable on arbitrary embedded parts.
- **Rates are exact rationals** (call/0007). The interval timer is `13125000/11` Hz. The integer figures
  usually quoted for it and for an event timer are *themselves* roundings, so a base derived from them is
  a base for clocks that do not exist. A register composes in the LCM base, after `flicks`.
- **The stored width was reversed from 64 to 128 bits mid-session, and the reason matters.** The narrow
  store was justified on proof cost. That was tested and disproved: multiplication over the full domain
  defeats the solver at *either* width, under SAT and under SMT, so the obligation needs a bounded domain
  regardless and width buys nothing. Meanwhile the narrow store capped how fine a base could be
  (five hours at femtosecond resolution) and made base derivation give up early. **Do not re-narrow it
  on a proof-cost argument without re-running the experiment.**
- **Multiplication is proved at boundary values, not exhaustively** (call/0009). The property is textbook
  interval arithmetic; what needs checking is the transcription, and that shows at the identities, the
  fitting boundary, and the extremes. Whole suite went from never-terminating to 49 seconds.
- An external SMT solver was installed, measured against the unbounded harness, found not to help, and
  removed. **Do not reach for a bigger solver here; the domain is the problem, not the back end.**
- Ordinal labels for the obligations were removed throughout after host-lint flagged every one of them.
  Obligations are named for what they assert. Never number them.

### 2026-07-28 — the interrupt verdicts

- Built latency and overrun in calx-telltale, pinned at `ce43c75`. 42 tests, 14 proof harnesses.
- **Overrun is the verdict that bites**, not latency: a handler dropping ring entries fails without
  ever missing a latency figure. A window of length `b` admits at most `floor(b / gap) + 1` arrivals,
  counting the one that lands as the window opens.
- **A withheld verdict is a verdict, not an error** (call/0008), and it is the design's whole point. A
  blackout counted in loop passes, because its clock was mid-reconfiguration, has no path to a deadline
  in nanoseconds. The tool names the missing piece and declines. `Sweep` reports the withheld count so
  an exclusion never reads as a clean pass. **Do not "improve" this by defaulting to a rate.**
- `PLAN.md` now runs 0002, 0005, 0001, 0003, 0004. The response-time work moved ahead of termination per
  call/0008: it needs composition rather than loop contracts. Folder numbers are identity, so only the
  index moved.

### 2026-07-28 — naming, and a third persona

- **The buffer verdict is `overrun`, never `occupancy`.** calx-mill spends `occupancy` on concurrency
  saturation: a percentage, steady-state, about resident parallelism. This is a transient burst against a
  fixed depth. Same word, different quantity, and this crate names calx-mill as its design sibling, so the
  collision would have misled rather than helped. `overflow` was unavailable too: it already means
  arithmetic leaving the width, in `Refusal` and `Missing`. Recorded in call/0008.
- **Check a sibling project's vocabulary before taking a word.** This was caught by the operator, not by
  a gate. host-lint sees naming tells, not cross-repository homonyms.
- Cast gained [Marek](cast/marek.md), the firmware engineer, on a **role** axis rather than a modality
  one. Marek shares Ines's modality and wants the opposite thing: Ines asks whether a projection can be
  trusted, Marek asks what to change. A verdict can be trustworthy and still useless by naming no
  declaration anyone can open. Recorded in call/0003.

### 2026-07-28 — real-time metrics, after consulting the cast

- Consulting the cast against a real-time workload found the register missing the term everything hangs
  off: **handler execution time**. Without it there is no response time, no utilisation, and no
  schedulability statement. The tool could say a burst overruns a ring; it could not say a handler taking
  longer than its own period never catches up, which needs no blackout at all.
- **The WCET objection dissolves**: the cost is *declared*, not derived, like every other number, and an
  `Assumed` cost makes the verdict `Assumed`. Refusing to derive a quantity is not the same as pretending
  it does not exist, and the second is the more damaging refusal. Recorded in call/0010.
- call/0010 **supersedes the priority exclusion in call/0008**. Response time is the standard
  fixed-priority fixed point `R = C + B + Σ ceil(R/T)·C`, and **the blocking term B is the blackout
  window this project already computes** — that is where the two halves of the tool meet.
- Priority is numbered **the way the hardware numbers it: lower preempts higher** (NVIC convention), not
  the literature's opposite. A register transcribed from a datasheet must read correctly.
- **Two record/code divergences were found and closed**, both the anti-pattern the operator named
  earlier: call/0008 promised a drop consequence the struct never had, and the founding note required
  exclusions stated on every run with nothing implementing it. `limits.rs` now enumerates nine.
- Proof coverage is deliberately partial: harnesses hold provenance flow, the never-judged case, and that
  priority is a strict order (which is what lets the recurrence partition the set). The recurrence itself
  walks a heap-allocated set and is test-covered, on call/0009's reasoning.

### 2026-07-28 — composition and attainment

- Built the five expression forms and the attainment search, pinned at `5538b4e`. 63 tests, 17 harnesses.
- **Moved 0003 ahead of 0001** in PLAN.md: the register format has to serialise the expression tree, so
  designing the schema first would have been guesswork. Same dependency argument that moved 0005 ahead
  of 0004.
- **Evaluation is parameterised by one latency**, meaning how long the outside world takes to answer.
  Every wait in a composition sees the same world, so a cost is a function of that parameter. This is
  what makes attainment a search rather than an arithmetic step.
- **The search domain is bounded by the widest budget, never by cost.** Costs run to millions; the domain
  stays a few hundred wide, because past the widest budget every wait has already given up. Do not
  "optimise" this by sampling: the whole point is that the maximum is interior.
- The short-circuit boundary is where a guard stops succeeding. One step earlier the body runs and the
  cost compounds; one step later everything unwinds. **An off-by-one there moves the worst case**, which
  is why the harnesses hold exactly that leaf boundary.
- Rename leftover found: `PLAN.md` still said "Occupancy" after the overrun rename, because it was not in
  the file list. **A rename sweep must include PLAN.md and MEMORY.md, not just call/ plan/*/ and src/.**

### 2026-07-28 — the plan completed to the lifecycle, and v0.2.0

- Milestones 0004 (termination, counter fit) and 0001's register format and command line are built.
  75 tests, 19 harnesses. **calx-telltale v0.2.0 released**, pin `191675b`, artifact `65001736`.
- **The release phase is `done`**, which discharges the calx-telltale skip in call/0004. Its own
  condition was "the first milestone producing a binary", and the CLI was that milestone.
- `host-lifecycle release` **computes the version from a change class**, never a semver level chosen by
  hand: `adds-flag` mapped 0.1.0 to 0.2.0. It runs verify, bumps, builds in the pinned container, derives
  the canonical hash, then **stops and prints the outward steps for a human**. Tagging is operator-run by
  design; do not assume authority for it.
- **The zero-dependency design paid off at release time**: the offline build inside the pinned container
  needed no vendored bundle, which host-lint and the other components do need.
- 0004 came in cheaper than its milestone expected. It assumed loop contracts; declaring how the counter
  moves turned termination into a property of the declaration rather than a search over the loop. **Loop
  contracts are still the right tool for attainment**, which searches a domain.
- Outstanding: the ELF census adapter (needs the crate's first dependency, so placement wants a decision
  first), the armed-interval comparison, and monotonicity reported as a proved property.
- The gh account reverted mid-sequence again and the tag push failed as connollydavid. See
  [[gh-account-switching-for-slartibardfast-pushes]]; re-check identity before every outward step.
