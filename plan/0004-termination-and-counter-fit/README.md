# 0004 termination-and-counter-fit

Well-foundedness for every declared wait, and a budget that fits the counter
holding it.

- **Who**: [Marek](../../cast/marek.md), who wrote the wait and will not act on a
  verdict that names no declaration, and [Ines](../../cast/ines.md), who needs to
  know that a declared timeout can actually fire.
- **What**: the acceptance below, and the obligations it discharges: termination and counter fit.
- **Why**: a wait whose measure wraps has a bound in its documentation and none
  in its behaviour.

## The increment

### Termination

For every `Wait`, the measure is well-founded and strictly decreasing on every
path through the body. This is expressed with a `loop_decreases` clause, which
is the reason the approach is worth its cost: without it, a loop contract
establishes partial correctness only and says nothing at all about
non-termination. A teardown loop that states a bound of ten thousand,
post-decrements its counter, and then tests it against zero fails termination by
construction, because the counter wraps past the test.

### The budget fits its counter

The declared budget is at most the counter type's maximum, checked at
declaration. A retry counter held in a `uint16_t` carrying a budget of `0x2000`
passes today, and sits one doubling away from wrapping in silence.

This milestone needs loop contracts rather than unrolling, because realistic
budgets run to thousands of iterations and unrolling them is not viable. That is
why it sits after the two milestones whose harnesses are small and bounded.

## Where it stands

**Built**, and more cheaply than this milestone expected. The entry assumed loop
contracts, because proving termination of a loop is a statement about the loop.
Declaring how the counter moves changes the question: a counter tested after it
moves never sees zero, whatever it counts to, so the finding is a property of the
declaration rather than a search over the iteration space. Both obligations
therefore quantify over everything their types admit and discharge in seconds.

The loop contracts are still the right tool for attainment, which searches a
domain rather than reading a declaration. That is where the expectation belongs.

## Acceptance

- A wait declaring a post-decrement measure with a zero test fails termination, and the
  failure names the declaration.
- Raising a budget above its declared counter type's maximum fails counter fit.
- A wait with a well-founded decreasing measure discharges termination without unrolling
  its full budget.
