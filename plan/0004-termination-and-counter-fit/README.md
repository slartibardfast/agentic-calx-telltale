# 0004 termination-and-counter-fit

Well-foundedness for every declared wait, and a budget that fits the counter
holding it.

- **Who**: [Ines](../../cast/ines.md), who needs to know that a declared timeout
  can actually fire.
- **What**: the specs under `spec/`, which discharge K1 and K2.
- **Why**: a wait whose measure wraps has a bound in its documentation and none
  in its behaviour.

## The increment

### K1, termination

For every `Wait`, the measure is well-founded and strictly decreasing on every
path through the body. This is expressed with a `loop_decreases` clause, which
is the reason the approach is worth its cost: without it, a loop contract
establishes partial correctness only and says nothing at all about
non-termination. A teardown loop that states a bound of ten thousand,
post-decrements its counter, and then tests it against zero fails K1 by
construction, because the counter wraps past the test.

### K2, the budget fits its counter

The declared budget is at most the counter type's maximum, checked at
declaration. A retry counter held in a `uint16_t` carrying a budget of `0x2000`
passes today, and sits one doubling away from wrapping in silence.

This milestone needs loop contracts rather than unrolling, because realistic
budgets run to thousands of iterations and unrolling them is not viable. That is
why it sits after the two milestones whose harnesses are small and bounded.

## Acceptance

- A wait declaring a post-decrement measure with a zero test fails K1, and the
  failure names the declaration.
- Raising a budget above its declared counter type's maximum fails K2.
- A wait with a well-founded decreasing measure discharges K1 without unrolling
  its full budget.
