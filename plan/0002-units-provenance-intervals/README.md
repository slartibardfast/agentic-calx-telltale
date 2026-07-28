# 0002 units-provenance-intervals

The `Quantity` core: an interval, a unit, and a provenance, with the three
obligations that hold them together.

- **Who**: [Ines](../../cast/ines.md), who needs to tell a derived number from a
  guess while reading one result.
- **What**: the acceptance below, and the obligations it discharges: unit soundness, provenance monotonicity and interval soundness.
- **Why**: [call/0003](../../call/0003-cast-modalities.md) requires the
  provenance to reach both the printed report and the structured output.

## The increment

Every number in the model is a `Quantity`. Units are typed and closed. `Ticks`
and `Cycles` name the source they are counted against, so two differently-clocked
quantities cannot be confused and a rate never enters the model as a bare
integer ([call/0006](../../call/0006-sources-are-declared-and-delays-name-them.md)).
`Iterations` and `BusReads` have no conversion to time, and supplying one
requires a declared `Conversion` carrying its own provenance.

A source is where a rate's provenance enters. Converting ticks to a time reads
the source's nominal frequency, which is itself a `Quantity`, so a frequency
somebody guessed makes every time derived from it `Assumed` with no further
wiring. Where a wait's window overlaps an interval in which its source is
declared invalid, the conversion is refused rather than taken against a stale
rate.

A provenance is `Derived`, `Extracted`, `Measured` or `Assumed`. The governing
rule is that a result carries the weakest provenance among its inputs. This is
the property the tool is named for, and it is the one that would have caught a
guessed bus latency contaminating a derived poll count.

## Obligations

### Unit soundness

Combining quantities of different units requires a declared conversion. The type
system carries most of this, and the residue goes to Kani.

### Provenance monotonicity

For every operation, the provenance of the result is at most as strong as the
weakest provenance among its inputs. One `Assumed` input therefore makes the
whole answer `Assumed`, wherever that answer is printed.

### Interval soundness

Interval arithmetic is conservative: the result interval contains the result of
every point-valued evaluation within the input intervals. An interval that
narrowed under composition would be claiming a precision its inputs never had.

All three are small bounded harnesses with modest unwinding, so this milestone
does not wait on loop contracts.

## Where it stands

**Built.** The `Quantity` core holds an interval, a unit and a provenance, and
the three obligations are proved over everything their types admit. Every stored
count is a `u128` behind a single alias, and arithmetic refuses rather than
wraps.

The width was reversed here after the fact, and the reasoning is kept in
[call/0007](../../call/0007-rates-are-exact-rationals.md): a narrower store was
chosen to make the proofs cheaper, the saving was measured and found not to
exist, and the narrow store had meanwhile been capping how fine a base could be.

## Acceptance

- A conversion applied without a declaration fails to compile or fails unit soundness.
- A composition mixing one `Assumed` input with three `Derived` ones reports
  `Assumed`, in both the human report and the structured output.
- Replacing the provenance join with a maximum fails acceptance.
