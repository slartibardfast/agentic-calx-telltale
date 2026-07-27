# agentic-calx-telltale

The agentic host governing
[calx-telltale](https://github.com/slartibardfast/calx-telltale), a verified
calculator for waits, interrupts-off windows, and the compositions built out of
them. This repository holds the *thought* about that software, which is to say
its personas, its milestones, its decisions and its verification wiring. The
software itself lives in the Where room, materialised from `.host-software`
rather than committed. The two were adopted together from
[connollydavid/host](https://github.com/connollydavid/host) at template revision
`c8fc28c`, recorded in [call/0001](call/0001-adopt-host-template.md), and the
host has since taken the ledger forward to the reference gate
([call/0005](call/0005-upgrade-to-the-reference-gate.md)).

## The rooms

| Question | Room | Holds |
|----------|------|-------|
| Who  | `cast/` | [Ines](cast/ines.md) and [Tarn](cast/tarn.md), the two modalities the tool serves |
| What | `plan/<milestone>/spec/` | specs: behaviour (`.allium`), timing (`.tla`) |
| When | `plan/` | the milestone index and folders |
| Where | `software/calx-telltale/` | the software, materialised from `.host-software` |
| Why  | `call/` | decisions, in MADR format |
| How  | `CLAUDE.md` + `tools/` | the operating manual and the verification tools |

`CLAUDE.md` is the operating manual and the thing to read first. `STRUCTURE.md`
is the one-page map.

## Where the work stands

The verified core exists: quantities carrying units and provenance, exact
rational clock rates, the base a register composes in, the tree of derived
clocks, and the delay forms that bind a wait to a clock. The register format,
the command-line surface and the interrupt verdicts are still ahead.

The milestones in [plan/PLAN.md](plan/PLAN.md) run in an order that carries a
judgement. Both errors that motivated the tool were errors of arithmetic and of
carried provenance, so the milestones addressing those come first, ahead of the
ones that wait on loop contracts. Interrupt response time has since moved ahead
of the termination work as well, because it depends on composition rather than
on contracts and it is the question the whole enquiry began with
([call/0008](call/0008-interrupts-are-declared-and-answerability-is-a-verdict.md)).

The founding design note is deliberately not held in this repository.
[call/0002](call/0002-design-note-stays-out-of-repo.md) records why, and what
crossed into the host in its place.

## Working here

A fresh clone carries neither its submodules nor its Where room, and the gates
need both, so start by bootstrapping:

```bash
git clone --recurse-submodules https://github.com/slartibardfast/agentic-calx-telltale
cd agentic-calx-telltale
host-lifecycle bootstrap .
```

That initialises the submodules, materialises the software from
`.host-software`, generates the skill symlinks with `link-skills.sh`, builds the
gating tool, and installs its commit hooks. The software's own suite and proofs
run from `software/calx-telltale/main` with `cargo test` and `cargo kani`.

## License

Released into the public domain under the [Unlicense](LICENSE). The software it
governs carries the same terms.
