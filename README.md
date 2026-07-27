# agentic-calx-telltale

The agentic host governing
[calx-telltale](https://github.com/slartibardfast/calx-telltale): a verified
calculator for waits, interrupts-off windows, and the compositions built out of
them.

This repository holds the *thought* about that software. Its personas, its
milestones, its decisions and its verification wiring live here. The software
itself lives in the Where room, materialised from `.host-software` rather than
committed.

Adopted from [connollydavid/host](https://github.com/connollydavid/host) at
template revision `c8fc28c`; see [call/0001](call/0001-adopt-host-template.md).

## The rooms

| Question | Room | Holds |
|----------|------|-------|
| Who  | `cast/` | [Ines](cast/ines.md) and [Tarn](cast/tarn.md), the two modalities the tool serves |
| What | `plan/<milestone>/spec/` | specs: behaviour (`.allium`), timing (`.tla`) |
| When | `plan/` | the milestone index and folders |
| Where | `software/calx-telltale/` | the software, materialised from `.host-software` |
| Why  | `call/` | decisions, in MADR format |
| How  | `CLAUDE.md` + `tools/` | the operating manual and the verification tools |

`CLAUDE.md` is the operating manual; read it first. `STRUCTURE.md` is the
one-page map.

## Where the work stands

The design is settled and no implementation exists yet. The milestones in
[plan/PLAN.md](plan/PLAN.md) run in an order that carries a judgement: the two
errors that motivated the tool were errors of arithmetic and of carried
provenance, so the milestones addressing those come first, ahead of the ones
that need loop contracts.

The founding design note is deliberately not held in this repository.
[call/0002](call/0002-design-note-stays-out-of-repo.md) records why, and what
crossed into the host in its place.

## Working here

A fresh clone needs its submodules and its Where room before the gates run:

```bash
git clone --recurse-submodules https://github.com/slartibardfast/agentic-calx-telltale
cd agentic-calx-telltale
host-lifecycle bootstrap .
```

That initialises the submodules, materialises the software from
`.host-software`, generates the skill symlinks with `link-skills.sh`, builds the
gating tool, and installs its commit hooks.

## License

Released into the public domain under the [Unlicense](LICENSE).
