# 0002 units-provenance-intervals

The `Quantity` core: an interval, a unit, and a provenance, with the three
obligations that hold them together.

- **Who**: [Ines](../../cast/ines.md), who needs to tell a derived number from a
  guess while reading one result.
- **What**: the specs under `spec/`, discharging K5, K6 and K7.
- **Why**: [call/0003](../../call/0003-cast-modalities.md) requires the
  provenance to reach both the printed report and the structured output.

## The increment

Every number in the model is a `Quantity`. Units are typed and closed. `Ticks`
and `Cycles` carry their rate so that two differently-clocked quantities cannot
be confused. `Iterations` and `BusReads` have no conversion to time, and
supplying one requires a declared `Conversion` carrying its own provenance.

A provenance is `Derived`, `Extracted`, `Measured` or `Assumed`. The governing
rule is that a result carries the weakest provenance among its inputs. This is
the property the tool is named for, and it is the one that would have caught a
guessed bus latency contaminating a derived poll count.

## Obligations

### K5, unit soundness

Combining quantities of different units requires a declared conversion. The type
system carries most of this, and the residue goes to Kani.

### K6, provenance monotonicity

For every operation, the provenance of the result is at most as strong as the
weakest provenance among its inputs. One `Assumed` input therefore makes the
whole answer `Assumed`, wherever that answer is printed.

### K7, interval soundness

Interval arithmetic is conservative: the result interval contains the result of
every point-valued evaluation within the input intervals. An interval that
narrowed under composition would be claiming a precision its inputs never had.

All three are small bounded harnesses with modest unwinding, so this milestone
does not wait on loop contracts.

## Acceptance

- A conversion applied without a declaration fails to compile or fails K5.
- A composition mixing one `Assumed` input with three `Derived` ones reports
  `Assumed`, in both the human report and the structured output.
- Replacing the provenance join with a maximum fails acceptance.
