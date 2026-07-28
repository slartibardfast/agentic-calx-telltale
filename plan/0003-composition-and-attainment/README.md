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

## Where it stands

**Built**: the five expression forms, evaluation at a latency, and the search
over the domain that returns both the worst case and the latency attaining it.
A composition reports whether its maximum is interior, which is the finding a
sweep of the extremes would have missed.

The domain is bounded by the widest budget in the composition rather than by any
cost, so the search stays small even where the costs run to millions. Past that
bound every wait has already given up, and nothing further can change.

**Proof coverage**: the harnesses hold the leaf semantics the whole tree rests
on, which are that a wait succeeds exactly while the answer arrives inside its
budget, and that it never polls past that budget. The short-circuit operator
turns on that boundary: one step earlier the body runs and the cost compounds,
one step later the whole composition unwinds, so an off-by-one there would move
the worst case. The tree itself is heap-allocated and carried by tests, on the
reasoning in [call/0009](../../call/0009-multiplication-is-proved-at-the-boundaries.md).

**Monotonicity is now established rather than assumed.** A composition reports
whether its answer was established or searched for. Where the cost cannot fall,
the worst case is at the top of the domain by construction and no search runs.

The claim rests on the leaf, and the leaf is proved: a wait polls
`min(latency, budget)` times, so its cost never falls as the world gets slower.
From there it propagates structurally. A branch of monotone arms stays monotone,
and a sequence, a retry or a short circuit takes the whole composition into the
searched case, because each of them stops early.

The rule is conservative in the safe direction: anything that might stop early
is treated as stopping early, so monotonicity is never claimed for a composition
that could break it. A test holds the established answer to what the search
would have found.

## Acceptance

- A `ShortCircuit` whose guard succeeds expensively reports the expensive
  branch, and reports the witness that attains it.
- The witness is found by the tool rather than supplied to it, and it is
  reported for an input strictly inside the declared budget.
- Mutating `ShortCircuit` to a product fails acceptance.
