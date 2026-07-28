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

### 2026-07-28 — pushing to this pair no longer depends on the gh account

- `git push` in this host and in `software/calx-telltale/main` now works whatever account `gh` thinks is
  active. The switch-and-inline-credential dance used through this session is retired.
- **Root cause**: `~/.gitconfig` carries `credential.https://github.com.helper=` (empty, which clears the
  chain) followed by `!/usr/bin/gh auth git-credential`, so the global `store` helper was bypassed for
  every github.com push and gh's helper was the only one running. That helper reads gh's *active*
  account, which reverted on its own several times. `gh auth setup-git` writes this.
- **Fix**, repo-local so it leaves `connollydavid` repositories alone: name the user in the remote
  (`https://slartibardfast@github.com/...`) so the credential store matches on username, then clear and
  restore `store` for github.com in each repo's own config. The software repo's config lives in the bare
  store, so every worktree inherits it.
- `gh` itself still follows the active account, so anything needing `slartibardfast` identity through
  `gh` (creating a repo, say) still wants a switch and a check of `gh api user --jq .login` first.

### 2026-07-28 — the tool describes itself

- Asked whether calx-telltale was self-describing for agentic use. It was not, and the check was empirical
  rather than assumed: `--help`, `-h` and `--version` all exited 2, so an agent's first three guesses all
  failed.
- **The serious gap was not the help flag.** call/0003 owed Tarn "a stable machine-readable output shape
  carrying provenance as a field". The exit codes had been built and the structured output never was, so
  every surface was prose. **That is the third record/code divergence of this kind found in this project**
  — after the drop consequence and the stated exclusions. When a record promises a surface, check the
  surface exists.
- Now: `--json` emits one object with the same facts as fields, including per-finding provenance, the
  weakest standing across the run, the exit code, and the limits. An unreadable register reports in the
  same shape. A `grammar` command prints the register format, so an agent holding only the binary can
  author a register without the repository.
- The JSON encoder is hand-written. **Do not take a serialisation dependency**: the offline reproducible
  build in the pinned container works precisely because the dependency list is empty, which no other
  component here manages.
- Pin `ccb62cd`, artifact `32875ac`. **The pin is now ahead of the v0.2.0 tag**, and the added CLI surface
  is an `adds-flag` change, so the release phase wants re-running when the operator wants another tag.

### 2026-07-28 — the plan extended, after consulting the cast

- **The published README advertised seven subcommands and the binary answered to one of them.** census,
  project, attain, deadline, overrun and diff were all documented and absent. **The fourth record/code
  divergence in this project**, and the largest, since this one was public. The table now marks the
  unbuilt verbs as planned.
- The arithmetic behind four of them already exists unwired (`Expr::attain`, `Interrupt::latency`,
  `Interrupt::overrun`, `Schedule::response_time`), so the gap is surface rather than substance. That is
  why 0007 is cheap and high value.
- **Consulting the cast found a fifth gap**: cast/marek.md promises "a declaration that names the file and
  symbol it was extracted from", and the register format has no such field. `Provenance` says *how* a
  value was arrived at and never *where*. The split was deliberate (call/0006 keeps free text outside the
  proof boundary) and the consequence was overlooked, because the register never grew the fields the
  reporting layer was meant to hold.
- Plan now runs 0002, 0005, 0003, 0001, 0004, then **0006 citations, 0007 verbs, 0008 census**. Citations
  come first so a verdict carries one from its first version rather than gaining one afterwards; building
  the verbs first would mean reworking all of them.
- 0008 waits on a decision, not on code: the object-format reader would be the core's **first
  dependency**, and the empty dependency list is what makes the offline release build work with no
  vendored bundle. The adapter belongs beside the core, and that placement wants a call/ record first.
- **The non-disclosure constraint in call/0002 is lifted by operator ruling.** The note was already
  heavily redacted and was never committed. Acceptance criteria stay stated as properties anyway, because
  a property holds across images and a figure holds for one.

### 2026-07-28 — every plan milestone closed, and v0.3.0

- All eight open items closed. calx-telltale **v0.3.0** released, pin `ac3175d`, artifact `babfff8`.
  91 tests, 20 harnesses, both lanes green, 0 hazards.
- **call/0011**: the census adapter sits beside the core as a workspace member, out of the canonical
  artifact, which is built with `-p calx-telltale`. The record drew the line at *parsing is bounded,
  disassembly is not* — so the ELF reader is hand-written and the adapter ended up with **no external
  dependencies either**, which is better than the record anticipated.
- **0006 citations**: a wait carries `file=`/`symbol=`; a failing verdict now names a site rather than an
  identifier. Citations sit *beside* the declarations so the verified types stay free of text.
- **0007 verbs**: project, attain, deadline, overrun, diff, each with `--json`. The register grew
  compositions (operands by reference, resolved top down), interrupts and windows.
- **0005 remainder**: armed deadlines get their own `Unarmed` verdict — *an unbounded span is not a pass*
  and counts with the withheld. Jitter widens the burst window. A handler declaring `reenables` is
  interfered with by its own priority level. Two limits became model.
- **Correlated failure carried into the arithmetic**: two clocks off one root are exact multiples, so the
  root's error cancels in a comparison between them. `exact_ratio` returns the exact ratio where a root
  is shared and `None` where it is not. The limit narrowed to *common-mode excursion*, which is the root
  leaving its own tolerance — a genuinely different failure.
- **0003 monotonicity**: established rather than searched where the cost cannot fall, resting on a
  **proved** leaf case and propagating structurally. Conservative in the safe direction; a test holds the
  established answer to what a search would have found.
- **0008 census**: drafts a register from an image's function symbols with citations and `?` blanks.
  Emits candidates rather than waits, and **says so in its own header**, because what it misses without a
  disassembler it misses silently.

- A clock now carries a citation too, which was the last of 0006's open work: the frequency is the value
  most worth tracing, since everything timed by that clock rests on it.
- **A `sed` on `.host-software` matched both stanzas** and gave host-lint a `-p calx-telltale` flag for a
  package it does not have. `software --verify-build` caught it, which is the gate earning its keep.
  **Edit that file by stanza, never by a pattern that appears in more than one.**

### 2026-07-28 — the encoding question, answered against a real part

- **"Which architecture" was underspecified.** The unit a branch decoder is written against is the
  *encoding*, not the instruction set: one set carries several encoding modes, width-changing extensions
  are the usual case rather than exotic, and endianness decides how a word is assembled.
- **The cost has three tiers, not two.** Strided (fixed width); length in a fixed prefix of the first
  halfword (Thumb-2, compressed RISC-V); and **length in the operand form**, where a reader must decode
  most of an instruction to find the next. The third tier is where writing a decoder stops being cheap.
- **ARC EM is the worked case and sits in the third tier.** Four widths; compact forms are the majority;
  a long-immediate word appended per the operand form gives the wider ones. **The limm word is two
  little-endian halfwords, high half first** — a flat 32-bit LE read finds nothing, and the failure looks
  like absence rather than error. Even a mature suite left roughly half its spans undecoded on a stripped
  image, clustering on a major opcode absent from the published set.
- **Delegation is a real alternative to decoding**: parse a toolchain listing and take boundaries from its
  address column. Far cheaper, works today, but needs a toolchain and cannot run on a bare blob — **and
  the register's soundness then rests on that toolchain's boundary-finding. A mis-lengthed instruction
  loses a back edge in a way nothing downstream can detect.** Now a declared limit, stated on every run.
- The ELF reader handles **both classes**; ARC EM is ELF32 and 64-bit-only would have declined the domain.
  The layouts differ in more than field width: a symbol's info byte moves, which reads plausible values
  from wrong offsets rather than failing.
- **Edit `.host-software` by key within its stanza, never by expected value.** Twice now a replacement
  keyed on an old literal silently matched nothing: once giving host-lint a flag it could not use, once
  leaving a stale pin so `verify-build` rebuilt the wrong commit and reported drift that was not there.
  There is a `setkey.py` pattern for this in the session scratchpad.

### 2026-07-28 — cleared for release

- Everything before a release is done. Pin `81d763e`, artifact `525915af`, gates green, both builds
  reproduce. Three commits sit ahead of `v0.3.0` and the change class is **adds-flag**, checked rather
  than assumed: the core delta is purely additive (`limits.rs`, `main.rs`, `register.rs`), nothing public
  removed.
- **The adapter's version is `0.0.0` and `publish = false`, deliberately.** It had drifted a release
  behind the core, which reads as neglect. It has never been released, call/0011 keeps it out of the
  canonical artifact, and call/0004 leaves open whether it is ever released alone. A number tracking the
  core would imply lockstep it does not have; one left behind reads as drift. **Zero says neither and
  needs no hand-sync at each release**, which is how the skew appeared. Recorded in call/0011.
- The README had still advertised drafting a register as a *planned verb of the core binary*. It is
  neither planned nor a verb: it is a separate built binary. Fixed, with a section describing what it
  declines to claim.
- Note for the next release: `host-lifecycle release` bumps only the package it is given, so a workspace
  member's version is never touched. That is fine while the adapter stays at zero, and would need
  handling if it ever gains a real version.

### 2026-07-28 — v0.4.0

- Released **v0.4.0**, pin `f3fd27d`, artifact `70288ff`. Change class `adds-flag`, verified additive
  before running the gate rather than asserted.
- **The zero-version decision held its first test**: the release bumped the core to 0.4.0 and left the
  adapter at 0.0.0 untouched, with no hand-sync needed and no skew created. That is the behaviour it was
  chosen for.
- `setkey.py` (set a field by key within a named `.host-software` stanza) was used for the re-pin and
  worked. Keep using it; the two defects this session both came from replacing by expected value.

### 2026-07-28 — the adapter was claiming a kind it had not established

- The draft emitted a `wait` declaration per **function symbol** — 1513 of them on a real image. A wait is
  a polling loop and a function symbol is not, so every line claimed a kind the adapter had never
  established, and the file did not parse anyway (it stopped at the first blank).
- Candidates are **commented out** now. A reader uncomments one once they have found a loop worth
  declaring, and until then the file parses as what it honestly is: a register declaring nothing.
- **What the adapter needs before it could be released** is the capability, not the packaging: instruction
  boundaries, which is milestone 0011. Packaging is small by comparison — its own `.host-software` stanza
  with a build and artifact (it has no external dependencies, so the offline build works), a real version
  in place of the deliberate zero, and a release receipt. **None of that is worth doing while the tool
  emits candidates rather than waits.**

### 2026-07-28 — the adapter finds loops

- `calx-telltale-census loops <listing>` reads a disassembly listing, groups instructions by function,
  recognises branches from a **per-architecture mnemonic set**, and takes those whose target sits at or
  before them inside the same function. On a real listing: **2899 loops across 476 functions** — a freeze
  set rather than a symbol dump.
- **call/0012: boundaries are delegated, not decoded.** Settled by the measured case rather than effort:
  on the part in hand a mature suite left half its spans undecoded, so a decoder would not merely have
  been expensive, it would not have worked. The cast agreed for its own reasons — Marek already has the
  toolchain, Ines gets something to curate, Tarn is owed clear failure when a listing is missing.
- **Never guess a branch from operand shape.** A load referencing an earlier address would read as a
  loop, and a false loop is worse than a missing one because it is a declaration a reader acts on.
  Unknown architecture is declined by name; there is a test for the load case.
- **call/0013: two crates in one repo defeat the release phase.** It materialises a checkout per
  component and bumps the *root* manifest, so releasing the adapter bumped the **core** to 0.5.0 under a
  census tag. Discarded before commit. The adapter keeps its component stanza (its artifact verifies and
  reproduces) and **skips release** — one repository, one cadence, and the core's tag covers both.
- The census stanza needed `Cargo.lock` refreshed after the version bump, or the offline `--locked` build
  refuses. Bump a workspace member's version and update the lock in the same commit.

### 2026-07-28 — the release sweep, and a tool that under-described itself

- **The binary dispatched ten verbs and its help listed five.** `project`, `attain`, `deadline`,
  `overrun` and `diff` were unreachable from `calx-telltale help`. The README carried the full set, so
  anyone checking the documentation saw nothing wrong; only running the tool showed the gap. This is the
  inverse of the defect 0007 was written to fix, and 0007's own acceptance ("a command that exists is
  reachable from the tool's own help") was unmet while the milestone read **Built**.
- **The lesson is about where a claim is checked.** `README` and `--help` restated the same list, and
  nothing compared them. The fix is a test that reads the dispatch arms out of `main.rs` and asserts each
  appears in `USAGE`, so the list is derived rather than restated twice. Verified by negative control:
  dropping a verb from the help fails the test by name.
- **`main.rs` had no tests at all** before this, which is why nothing caught it. A binary's own surface is
  worth a check even when the arithmetic beneath it is proved.
- **Eleven milestones pointed at empty `spec/` directories.** Every README said "the specs under
  `spec/`" and all eleven held only a `.gitkeep`. Scaffold text nobody replaced. Removed: a spec belongs
  beside the software it constrains, so an empty room under `plan/` invites the quarantine the spine
  forbids. `host-lifecycle validate` does not require the directories.
- **PLAN.md contradicted four of its own milestones**, all of which read Built in their own README. The
  index is the ordering authority, so it is the one file that must not be stale about the project's state.
- **Commit identity is not configured in either repo.** The host's local config says `host-lifecycle`
  (scaffold default) and the software worktree had none, yet every commit is authored
  `slartibardfast <david@connol.ly>`. Now set repo-locally in the host and in
  `software/calx-telltale/.bare`, so it stops depending on a per-commit override. Related: the credential
  fix in the per-user store.
- The software worktree has **no tracking upstream**, so `git push` alone fails there; use
  `git push origin HEAD:main`.
- **Re-recording an artifact hash has no flag.** `--lock` is for a `deps-bundle`, and `--verify-build`
  reports drift without recording. Rebuild the pin in the pinned container by hand and set `artifact` in
  `.host-software`. The census binary reproduced its old hash unchanged across the same commit, since the
  verbs that moved are not in it.

### 2026-07-28 — correction: the commit author is a person, not the account

- Corrects the identity bullet in *the release sweep* above. That entry recorded the repo-local
  identity as `slartibardfast <david@connol.ly>`, matching the authorship of the existing history.
  Wrong: `slartibardfast` is the **GitHub account the repositories live under**, and it is not how
  commits are authored.
- The author is **`David Connolly <david@connol.ly>`**, now set repo-locally in the host and in both
  bare stores (`software/calx-telltale/.bare`, `software/calx-telltale-census/.bare`).
- Commits before this entry carry `slartibardfast` as the author name and are **left alone**. The
  history is pushed, so rewriting it is a separate decision rather than a tidy-up.
- The lesson: an account name and an author identity are different facts that happen to share a
  credential. Copying one from observed history propagated the wrong one.

### 2026-07-28 — correction: we push as a collaborator, not as the owner

- Supersedes the credential workaround recorded under *pushing to slartibardfast repositories* above,
  and completes the author correction immediately before this entry.
- **`connollydavid` is a collaborator on both repositories** (`push=true`, `admin=false`, checked
  against the API). So there was never a need to borrow the owner's credential: the account already in
  use could push all along.
- The workaround is retired. Remotes carry the plain URL again
  (`https://github.com/slartibardfast/...`), and the repo-local credential helper overrides are removed
  from the host and both bare stores, so the global `gh` helper serves the active account. Write access
  to each repository was proved by pushing a throwaway tag and deleting it, rather than assumed from a
  dry run reporting "up-to-date".
- **What the earlier entry got wrong** was the diagnosis, not the symptom. The push failure was real and
  the URL-scoped helper in `~/.gitconfig` is exactly as described. The error text named a permission
  problem, and the response was to satisfy it as the owner instead of checking whether the account in
  hand already had access. The cheaper question comes first: ask what permission you hold before
  engineering around not holding it.
- The account and the author stay separate facts: the repositories live under `slartibardfast`, the
  work is done by `connollydavid`, and commits are authored `David Connolly <david@connol.ly>`.

### 2026-07-28 — following the template forward, and the one change held back

- The template had moved twelve commits past the adopted `32e655f`. `host-lifecycle upgrade` reported
  **nothing pending**, correctly: the ledger's last entry is still `GATE-refs-in-verify`, our baseline,
  so none of the twelve is a ledger action. **A quiet upgrade check does not mean the spine is current** —
  pin bumps and spine prose ride along outside the ledger, and only a diff finds them.
- **host-lint moved to v0.16.6** (`cc3ec6a`), the revision the template pins. That closed both hazards
  raised below: upstream had already recorded `lint-skill.sh` and `test-integration.sh` executable.
  Fixed at the source rather than patched here, which is the reference-don't-vendor rule paying off.
- **host-lifecycle upgraded itself 0.47.1 to 0.47.2 mid-session**, and 0.47.2 added a check that a
  script invoked as `./name.sh` must be recorded `100755`. A gate reading zero hazards then read three.
  The conditions were as old as adoption. **A moving gate can make yesterday's green meaningless**, so
  read the tool version alongside the verdict.
- `link-skills.sh` was one of them, in this repo, and `core.fileMode=false` here makes the on-disk bit
  a lie. Only `git ls-files -s` tells the truth; the fix is `git update-index --chmod=+x`.
- **The template's own `CLAUDE.md` warns under the prose lane of the host-lint it pins.** Its new
  section carries "harness" (ai-diction). Adopting the spine verbatim would have re-opened our verify
  receipt, and rewording it locally would break copy-at-version. **Held the spine at the older revision
  and raised it upstream** ([connollydavid/host#20](https://github.com/connollydavid/host/issues/20)),
  rather than take a red gate or a private fork of the methodology.
- The general shape, worth remembering past this instance: the template's docs are not gated by the
  lane the template ships, so this class can recur at any spine change.
- `connollydavid/host-template` has issues disabled; template defects go to `connollydavid/host`.

### 2026-07-29 — the held-back spine section lands

- Upstream reworded the flagged line at `e89d064` ("a branch that needs a controlling terminal the
  probe cannot open"), so the section that would have reddened this gate yesterday came across
  verbatim today. `host-lint --prose` at the pinned `cc3ec6a` now exits 0 on it.
- The spine is byte-identical to the template's again, and
  [connollydavid/host#20](https://github.com/connollydavid/host/issues/20) is closed with the
  adopter-side verification recorded on it.
- **This is what holding a change back is for.** Yesterday's three options were a red gate, a private
  fork of the methodology, or a stale spine. Staleness was the only reversible one, and it cost a day.
  Prefer it whenever the alternative is editing something you do not own.

### 2026-07-29 — two false claims in the census, found by testing rather than reading

- **A recursive call was reported as a wait.** Branch mnemonics were matched by *prefix*, and on most
  of these architectures a call opens with a branch's letters: `bl` under `b`, `jal` under `j`. A `bl`
  back to a function's own start satisfied "target at or before, inside the same function" and came
  out as `wait id=0 ...`. That is precisely the false loop [[call/0012]] says is worse than a missing
  one. RISC-V carried the same defect.
- **The fix is asymmetric, and that asymmetry is the point.** Calls are matched **whole**, branches by
  prefix, because ARC's `blt` also opens with `bl` and excluding calls by prefix would drop real loops.
  Getting this wrong in either direction is easy; there are now tests for both.
- **`lp` was inert and its comment claimed otherwise.** The ARC set named ARC's zero-overhead loop
  instruction with a comment saying it was listed "so that it is found". It could never be: `lp` marks
  a loop whose end lies *ahead* of it, so the back-edge rule rejects it. Verified with a listing before
  removing it. The gap it pretended to close is the same gap 0011 already recorded.
- **The lesson is about how these were found.** Both survived review because the code *said* it did the
  right thing. A comment asserting a behaviour is not evidence of it, and neither is a mnemonic sitting
  in a list. The reproductions took two minutes each.
- The loop count in plan/0011 ("thousands across hundreds of functions") was measured **before** this
  fix and counted calls, so it is now an upper bound until re-measured. The listing it came from is not
  in the repository.
- **Watch for stale binaries when testing.** `cargo build --release` at the workspace root does not
  rebuild the census member; the first reproduction "failed to fix" because of it. Build with
  `-p calx-telltale-census`. Same trap as the `--help` check earlier.
- **A release now requires `--authorized <ref>`** (host-lifecycle, call/0050): a resolvable
  `call/NNNN`, `plan/NNNN[#anchor]`, or `owner/repo#N`, recorded on the receipt, "because an approval
  spoken in a session window is not a record". This release cited `plan/0011`. `receipt --record` takes
  the matching `--authorization`.
