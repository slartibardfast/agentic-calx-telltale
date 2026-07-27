# A rate is an exact rational, and the division is deferred

- Status: accepted
- Scope: software
- Date: 2026-07-27

## Context and Problem Statement

[call/0006](0006-sources-are-declared-and-delays-name-them.md) makes a source
carry a nominal frequency. It does not say how that frequency is held, and the
obvious answers are all lossy.

The Programmable Interval Timer settles the question on its own. It runs at a
third of the NTSC colour-burst frequency, which is exactly `105/88` MHz, or
`13125000/11` Hz. No integer count of hertz represents it, so the `per_sec: u32`
the founding note sketched cannot express the single most common timer on the
architecture the operator named. Storing nanoseconds per tick fails the same way
and in the same place, because the period is 838.0952… nanoseconds.

Once a rate is stored rounded, every conversion that uses it starts from a value
that is already wrong, and the error compounds through a composition. Precision
lost in the rate is precision lost everywhere downstream of it, and a tool built
to report the standing of its own numbers cannot afford that.

## Prior art

Facebook's `flicks` addresses the same problem from the media end. It fixes a
single time base of 705600000 Hz, chosen so that the frame rates and audio
sample rates that matter divide it exactly. Conversions then become exact integer
arithmetic and no rounding error accumulates across a pipeline.

The same instinct shows up in the hardware this tool models. The High Precision
Event Timer does not report a frequency at all: it reports its period in
femtoseconds, in a capability register, which is a base fine enough to carry the
periods it needs to express.

## Decision

The `flicks` trick does not transfer wholesale, and it is worth being precise
about why. A fixed base works when the set of rates is known and finite, which is
true of media and false here. call/0006 makes the source set open by
construction, so an adopter's part-specific timer at some arbitrary division of a
phase-locked loop defeats any constant this project could pick in advance.

What transfers is the underlying move, which is to carry the rate exactly and
defer the division:

- A frequency is held as a reduced rational, a numerator and a denominator in
  hertz. The Programmable Interval Timer is `13125000/11` and loses nothing.
- Conversions are exact rational arithmetic in a wider intermediate, and they
  round only where an integer result is demanded.
- Where rounding is demanded, it goes outward. A lower endpoint takes the floor
  and an upper endpoint takes the ceiling, so interval conservatism holds by
  construction rather than by adding slack after the fact.
- A tolerance in parts per million widens the result, because a crystal has one
  and a bound that ignored it would be narrower than the hardware.

### The register's base, and the finest one that fits

`flicks` fixes its base for the world. This tool derives one per register, which
is the scope at which the rate set really is finite, and it prefers the finest
base it can hold. Where a register declares a set of sources, the tool derives
the base by default and reports it, rather than offering it as an extra.

A base is a frequency in which every declared source's period is a whole number
of base ticks. Reduce each declared frequency to `n/d` hertz, and any common
multiple of the numerators serves. The least of them is the coarsest base that
works, and it is already at least as fine as the finest source, so nothing is
lost by taking it.

Two of the timers the operator named work the example. The Programmable Interval
Timer runs at a third of the colour burst, exactly `105/88` MHz, which reduces to
`13125000/11` Hz. An event timer at four times the colour burst is
`157500000/11` Hz. The second numerator is twelve times the first, so the base is
157500000 Hz, a tick of about 6.35 nanoseconds. In it the interval timer's period
is exactly 132 ticks and the event timer's is exactly 11.

Note what the rounded figures would have done. The frequencies usually quoted for
those two parts, 1193182 Hz and 14318180 Hz, are both roundings of the rationals
above, and a base derived from them is a base for two clocks that do not exist.
Carrying the rate exactly is what makes the base correct rather than merely
plausible.

Composition then runs in base ticks, where it is exact integer arithmetic and
introduces no rounding at all. A quantity is converted once on the way in and
once on the way out, so a bound built from several sources widens only for the
tolerances actually declared, rather than for every division along the way.

The derivation is guarded. The least common multiple of arbitrary numerators
grows without bound, and a register can declare sources whose base does not fit
in the width the core holds. Where that happens the tool says so, names the
sources that forced it, and stays with per-conversion rational arithmetic, which
is correct but wider.

### Width: 128 bits stored, and no floating point

Every stored count is a `u128`, behind a single alias so the width is one point
of change. Arithmetic refuses rather than wraps: a computation that would leave
the width returns an error, whether it takes its headroom in a wider
intermediate or in a checked operation of the stored width.

**This reverses an earlier decision in this same record, and the reason is worth
keeping.** The store was first set at 64 bits, on the argument that interval
soundness is a property of multiplication, that a model checker bit-blasts what
it proves, and that a narrower multiply would therefore discharge more cheaply.
That argument was tested and it is false. Multiplication over the full domain
defeats the solver at 64 bits and at 128, with a SAT back end and with an SMT
one, so the obligation needs a bounded domain at any width and the width buys
nothing. The saving that justified the narrower store never existed.

What the narrower store did do was fight the design. The register composes in
the finest base it can derive, and the finer the base the shorter the span a
fixed width can express: a femtosecond base, which is the unit an event timer
reports its own period in, left about five hours. Base derivation suffered the
same way, and gave up on least common multiples it should have carried. A width
chosen to make proofs cheap was making the tool worse at the thing it exists to
do, in exchange for nothing.

The wide store still has a limit, and the tool still reports it. Where a
register's declared windows run past the span its base implies, it says so as it
derives the base.

No part of this is floating point at any width. Quad precision would reintroduce
exactly the rounding this record exists to remove, and an interval that claims to
contain every point evaluation cannot rest on a representation that rounds where
it chooses. A range problem here is answered with wider integers.

## Consequences

- Good: the most common timer on the architecture the operator named becomes
  representable, which the sketched schema could not manage.
- Good: conservatism is a property of the conversion rather than a margin
  somebody chose, so interval soundness is provable about the arithmetic itself.
- Also good: a tolerance has somewhere to live, so a bound reflects the crystal
  rather than the datasheet's headline figure.
- Bad: the intermediate arithmetic needs more width than the values do, and
  every conversion has an overflow path that has to be handled rather than
  assumed away.
- Neutral: the common-base derivation is optional. Nothing depends on it, and a
  register whose sources share no usable base loses only the exact cross-source
  comparison.
