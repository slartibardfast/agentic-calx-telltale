# Multiplication is proved at the boundaries, and the rung is spent elsewhere

- Status: accepted
- Scope: software
- Date: 2026-07-28

## Context and Problem Statement

Two of the obligations quantify over multiplication. One says the product of two
intervals keeps its endpoints in order, and the other says the product interval
contains the product of every point evaluation inside its inputs.

Neither finishes. Over the full domain the model checker bit-blasts a full
multiplier and does not return, and every escape tried made no difference: a
tighter ceiling on the operands, a narrower stored width, and a pinned external
SMT solver in place of the bundled SAT one. The last of those was given ten
minutes and did not come back. Bounding the operands does not help because the
bound prunes the search without shrinking the circuit, which is fixed by the
type of the operation rather than by the range of its inputs.

Meanwhile the obligations carrying this project's actual content all discharge
in seconds. Provenance monotonicity, unit soundness across clocks, and refusal
in place of wrapping are cheap, and they are the properties a reader would doubt.

## Decision

The multiplying harnesses quantify over a set of boundary values rather than
over the whole domain: the identities, the values either side of the point where
a product stops fitting the stored width, and the extremes.

This is a trade made with open eyes, and the reasoning is the part worth
keeping. The mathematics here is not in question. The product of two
non-negative intervals has the products of the corresponding endpoints as its
own endpoints; that is textbook interval arithmetic, and the non-negative case
is its easy half, since the general rule needs a minimum and a maximum over four
corner products precisely because signs can flip, which cannot happen where
every quantity is a count. No machine is needed to believe it.

What is in question is the transcription. Whether this implementation pairs the
endpoints correctly rather than crossing them, and whether it refuses where the
product no longer fits, are properties of the code and not of the arithmetic. A
crossed pairing or a missing refusal shows itself at the boundaries, so the
boundaries are what the harnesses quantify over.

No external solver is adopted. One was installed and measured before this was
settled, and it did not change the answer.

## Consequences

- Good: the suite discharges in under a minute, so the proofs run in the lane on
  every push rather than only where someone is willing to wait.
- Good: the verification budget goes to the obligations a reader would actually
  doubt, which is where the deeper rungs are worth declaring later.
- Good: the project takes on no solver dependency for a lemma that no solver was
  needed to establish.
- Bad: the multiplying obligations are proved over a chosen set rather than over
  everything, so a defect that shows only at some interior value would survive.
  Given the shape of the code, which multiplies endpoints and checks the result,
  such a defect is hard to construct and none is known.
- Neutral: the same reasoning will apply to attainment when it arrives, since a
  search over a declared latency domain is the same kind of problem.
