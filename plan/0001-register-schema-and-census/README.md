# 0001 register-schema-and-census

The declaration everything else computes over, and the adapter that drafts it.

- **Who**: [Ines](../../cast/ines.md), who curates a register rather than
  authoring one from nothing, and [Tarn](../../cast/tarn.md), who edits and
  re-checks it in a loop.
- **What**: the specs under `spec/`.
- **Why**: [call/0002](../../call/0002-design-note-stays-out-of-repo.md) records
  where the design this milestone implements is held.
  [call/0006](../../call/0006-sources-are-declared-and-delays-name-them.md) adds
  timing sources and delays to the schema this milestone defines.

## The increment

A register file declares sources, waits, windows, deadlines, compositions and
conversions. It is hand-authorable, diffable and reviewable, and it is the
artefact that gets committed alongside the image it describes.

A `Source` is a declared clock. It carries a nominal frequency as a `Quantity`,
a counter width, a read cost, and the interval over which it is trustworthy.
The core knows no particular clock: a Programmable Interval Timer, a High
Precision Event Timer and a part-specific timer an adopter alone has are all
declarations of the same shape. This milestone owns the declaration format and
ships worked examples rather than built-in variants.

A `Wait` carries a name, a budget, the counter type that budget is held in, a
measure, a cost per iteration, what happens on exhaustion, the interrupt state
it runs under, and its delay. The measure is first-class because a post-decrement
that wraps its counter and then tests against zero has a timeout that can never
fire, and the tool has to know which form it is looking at before it can say
anything about termination.

The delay is first-class because it is what binds a wait to a clock. A bare spin
names no source and yields a count that has no conversion to time. A calibrated
busy loop names the source its calibration came from. A timer-backed sleep names
its source, the granularity of one tick, and its rounding, because a primitive
that rounds up to a whole tick costs up to a full granularity more than it was
asked for and a bound has to take the ceiling.

`census` walks an ELF and emits a skeleton register. It can supply the loops,
the back edges, the interrupts-off regions and the accesses that reach
peripheral space. It cannot supply a budget or a measure, so it emits those as
blanks carrying no provenance, and a human fills them in. Everything the adapter
does emit is marked `Extracted` with the file and symbol it came from.

## Where it stands

**Built**: the register format with its parser, and the command line over it. A
register is line-oriented because it is committed beside the image it describes,
so it has to diff cleanly and review in a pull request, and a nested format
would read better while diffing worse. Every value carries where it came from,
so provenance enters at declaration rather than being attached afterwards. A
blank an adapter left is reported as a blank rather than as a missing field,
since one wants a decision and the other wants a correction.

The schema holds the no-bare-rates rule itself: a tick count naming no clock is
refused at parse time, so the arithmetic never has to catch it later.

The census adapter that this milestone left outstanding has since been built
under [0008](../0008-census-adapter/README.md), where the placement it was
waiting on is settled.

## Acceptance

- A register round-trips through parse and print without losing a field.
- A skeleton emitted by `census` carries `Extracted` provenance on every value
  it supplies, and a blank wherever a human decision is required.
- A register with a blank in a required position is refused with a message
  naming the declaration, rather than defaulted.
- The freeze set the adapter reports is documented as a lower bound, because
  indirect calls through ops tables leave reachability unresolved.
- A tick-valued or cycle-valued declaration naming no source is refused at parse
  time, so a rate can never reach the model as a bare integer.
- A worked register declaring a Programmable Interval Timer and a High Precision
  Event Timer parses, and neither is known to the core as a variant.
