# 0003 composition-and-attainment

The composition operators, and the witness that says where a maximum occurs.

- **Who**: [Ines](../../cast/ines.md), who needs a worst case found without
  having guessed where to look for it.
- **What**: the specs under `spec/`, which discharge accumulator fit and attainment.
- **Why**: this is the milestone that makes the tool worth more than a sweep.

## The increment

Five expression forms. A leaf is a wait. `Seq` sums. `Repeat` multiplies by a
declared count. `Alt` takes a maximum over branches. `ShortCircuit` is the
operator that hand-analysis gets wrong, and its worst case is

```
max( cost(guard fails), cost(guard succeeds) + cost(then) )
```

which is not a product. Where the guard fails the `then` never runs, so the
expensive case is the one where the guard succeeds expensively. A retry loop
above a guard that always succeeds at its last permitted attempt costs orders of
magnitude more than the same loop over a guard that fails fast, and the
attaining input sits one unit inside the budget rather than out at its boundary.

Evaluation therefore returns a cost together with a witness, which is the point
in the latency domain that produces the maximum. Monotonicity is never assumed.
Where the tool can establish it, it reports it as a proved property, and where
it cannot, it searches the domain.

## Obligations

### Composition fits its accumulator

The accumulator type is wide enough for every reachable composition, and the
tool checks that rather than hoping for it.

### Attainment

The reported maximum is a true maximum over the declared domain. A solver earns
its keep over a sweep precisely here.

## Acceptance

- A `ShortCircuit` whose guard succeeds expensively reports the expensive
  branch, and reports the witness that attains it.
- The witness is found by the tool rather than supplied to it, and it is
  reported for an input strictly inside the declared budget.
- Mutating `ShortCircuit` to a product fails acceptance.
