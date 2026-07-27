# Timing sources are declared, and a delay names the source it rides on

- Status: accepted
- Scope: software
- Date: 2026-07-27

## Context and Problem Statement

The founding design note gives `Unit` two rate-carrying forms. `Ticks` holds a
`per_sec` and `Cycles` holds an `hz`, and both are plain integers. The same note
opens its data model by stating that every number is a `Quantity` carrying an
interval, a unit and a provenance, and that the model holds no bare integers.

So the model contradicts its own governing rule at the one place the rule earns
its keep. A rate is exactly what turns a count into a time, which makes it the
value most likely to be guessed and the value whose provenance most needs to
reach the result. A `per_sec` written as `u32` cannot say whether it came from a
datasheet, a capability register read at run time, a calibration, or somebody's
recollection.

A rate is also not an identity. Two tick counts at the same nominal rate, taken
from different hardware, are not interchangeable, and nothing in a bare `u32`
stops the model adding one to the other.

The three timing envelopes that motivated this tool differ from each other
precisely in how their delay relates to a timing source. The first polls with a
loop whose period is undefined while the clock tree it depends on is being
reconfigured. The second sleeps on a primitive that rounds up to a whole tick.
The third spins with no delay at all. Those were recorded as incompatible units,
which is true but understates the finding: they are three different relationships
between a wait and a clock.

## Decision

### Sources are declared, and the core knows none of them

A `Source` is an entity declared in the register and referenced by
identifier. The verified core knows nothing of the Programmable Interval Timer,
the High Precision Event Timer, or any other particular clock. Those are
declarations that adopters write, ship, and share, in the same way that
calx-mill's core knows nothing of CUDA or x86 and takes a substrate as a set of
resource axes. A source an adopter invents on a part nobody else has works
exactly as well as one this project ships an example for.

A source declares:

- a **nominal frequency**, as a `Quantity`, so the rate carries its own interval
  and its own provenance;
- a **counter width**, because a counter that wraps inside a window under study
  is a hazard rather than a detail;
- a **read cost**, because reading a clock is itself work, and on the parts this
  tool is aimed at it can be a bus access;
- the **interval over which it is valid**, which is the clock-tree case made
  expressible.

`Unit::Ticks` and `Unit::Cycles` name a source instead of carrying a rate.
Converting either to a time goes through that source, so the conversion inherits
the source's provenance under provenance monotonicity without anything further being wired up. A
nominal frequency somebody guessed makes every time derived from it `Assumed`,
which is the behaviour the tool is named for.

### A delay names its source, or names none

`Delay` becomes first class, and it is what binds a wait to a clock:

- **none**, a bare spin. The cost is in bus reads or iterations and there is no
  conversion to time, because nothing here measures time.
- **spin**, a calibrated busy loop. It carries its per-iteration cost and the
  source that calibration was taken against.
- **sleep**, backed by a timer. It carries the source, the granularity of one
  tick, and the rounding the primitive applies.

Rounding is part of the model because a sleep that rounds up to a whole tick
costs up to a full granularity more than it was asked for, and a worst case has
to take the ceiling. A model that ignored the rounding would understate every
tick-quantized wait by up to one tick per call, which over a walk of thousands
of calls is the difference between a bound and a wish.

### Validity is interval-scoped, and invalidity refuses

A source declares when it is trustworthy. Where a wait's window overlaps an
interval in which its source is invalid, the tool yields a count in the source's
own unit and refuses to convert it to a time. It does not fall back on the last
known rate. This is the same refusal the note already requires for a unit with no
declared conversion, applied to a source that has one and cannot honour it, and
it is what makes the clock-reconfiguration envelope expressible rather than a
footnote.

### Representation

The verified core operates on numeric identifiers and integer intervals. Names,
citations and free text live in the register and in the reporting layer, outside
the proof boundary. A proof over a string-keyed model spends its budget on the
strings, and unit soundness and provenance monotonicity are properties of the arithmetic rather than properties
of the naming.

## Consequences

- Good: the three envelopes stop being three incompatible conventions and become
  one model with three delay forms.
- Good: correlated failure gets a home. A shared source is a correlation class,
  so the lacuna the note recorded as out of scope now has somewhere to attach
  when it is taken up.
- Good: no bare integers survive in the model, so the rule the note states in its
  first data-model paragraph holds throughout.
- Bad: the register gains a section, and every tick-valued or cycle-valued
  declaration has to name a source. A register that names none can express less
  than the note's original sketch could, which is the point, but it is still more
  to write.
- Neutral: the gap between a nominal frequency and the actual one is left to the
  source's interval and provenance. Drift, temperature and ageing are not modelled
  as their own axes, and a project that needs them declares a wider interval.
