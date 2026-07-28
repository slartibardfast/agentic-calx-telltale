# 0007 the-promised-verbs

The published documentation describes seven subcommands. The binary answers to
`check`, `limits`, `grammar`, `version` and `help`. This milestone closes that
gap by building what was promised.

- **Who**: [Ines](../../cast/ines.md), primary here. The verbs she works
  through are the ones missing, so the tool currently offers her the smallest
  part of what it computes.
- **What**: the acceptance below, discharged by the tests and proofs in the software repo.
- **Why**: the arithmetic already exists and only the surface does not, so this
  is the cheapest large gain available.

## The increment

`project` reports the worst-case cost of each declared composition. `attain`
reports the witness, meaning the latency the worst case occurs at, and whether
it was interior. `deadline` compares a projection against the deadlines armed
over the interval it covers. `overrun` reports whether a blackout delivers more
arrivals than a buffer holds. `diff` compares one register against another, so a
change that moves a bound reaches review.

The library already computes every one of these. Evaluation and attainment are
in the expression tree, the latency and overrun verdicts are on the interrupt,
and utilisation and response time are on the schedule. What is missing is
argument handling and output, so the work here is a surface over settled
arithmetic rather than new arithmetic.

Every verb answers to the structured surface as well as the prose one. Today
only two commands do, which leaves the agentic reader served by a fraction of
the tool.

## Consulting the cast

- **Ines** gets her working loop, which is to price a composition and then ask
  where the worst case is attained. She has neither verb today.
- **Tarn** needs the structured surface on every verb rather than on two, and
  `diff` is his regression loop: run against the previous register, compare,
  decide whether a moved bound is a regression.
- **Marek** gets `deadline` and `overrun`, which are the verdicts he acts on.
  They depend on 0006, because a verdict he cannot trace to code is one he will
  route around.

## Ordering

This sits behind 0006 deliberately. Every verb here prints a verdict, and a
verdict should carry its citation from the first version rather than gain one
afterwards. Building the surface first would mean reworking all of it.

## Where it stands

**Built**: `project`, `attain`, `deadline`, `overrun` and `diff`, each answering
to the structured surface as well as the prose one. The register grew the
declarations they operate on, which are compositions, interrupts and blackout
windows. A composition names its operands by reference and resolves top down, so
a line-oriented file expresses a tree without nesting.

The worked example earns its keep here. `attain` reports a maximum sitting one
step inside a budget of 8192, which is the interior case the whole search exists
to find. `deadline` and `overrun` then disagree about the same window: the
blackout is inside the deadline and still overruns the buffer, which is the
failure a latency figure never shows.

`census` has since been built under
[0008](../0008-census-adapter/README.md).

**Outstanding**: a composition mixing units is refused by the arithmetic rather
than named at parse time, so the message points at the operation rather than at
the line that wrote it.

## Acceptance

- Each of the five verbs runs, and each answers to the structured surface.
- The documentation describes only commands that exist, and a command that
  exists is reachable from the tool's own help.
- A verb that cannot answer says which declaration was missing, rather than
  returning a number that rests on nothing.
- `diff` reports a moved bound as moved rather than as a new finding, so a
  reviewer sees the change rather than the state.
