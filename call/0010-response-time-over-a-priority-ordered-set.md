# Response time over a priority-ordered set

- Status: accepted
- Scope: software
- Date: 2026-07-28
- Supersedes: the priority exclusion in
  [call/0008](0008-interrupts-are-declared-and-answerability-is-a-verdict.md)

## Context and Problem Statement

[call/0008](0008-interrupts-are-declared-and-answerability-is-a-verdict.md) put
interrupt priority, preemption and nested handlers outside the model, and judged
a declared interrupt against blackout windows alone. Consulting the cast against
a real-time workload showed that exclusion cuts out the middle of the subject
rather than an edge of it.

The register declares how often an interrupt arrives, what latency it can
absorb, and how deep its buffer is. It does not declare **how long the handler
runs**, and without that term there is no response time, no utilisation, and no
statement about whether the system keeps up at all. The tool can say that five
arrivals queue in a buffer four deep. It cannot say that a handler taking longer
than its own arrival period will never catch up, which is a more common failure
and the one [Marek](../cast/marek.md) meets first. That failure needs no blackout
window to occur.

The standing objection is that execution time belongs to worst-case execution
time analysis, which the founding note puts out of scope and this project has no
business attempting. That objection dissolves on inspection. The tool does not
have to **derive** an execution time. It accepts a **declared** one carrying its
own provenance, exactly as it accepts a declared frequency or a declared budget,
and a handler cost somebody guessed makes every verdict resting on it a guess.
Refusing to derive a quantity is not the same as pretending the quantity does
not exist, and the second is the more damaging refusal.

## Decision

An interrupt additionally declares an execution cost, a priority, and the
consequence of a dropped arrival. The verdicts extend to the standard
fixed-priority response-time analysis over the declared set.

### The recurrence

Worst-case response time is the fixed point of

```
R = C + B + Σ_{j of higher priority} ceil(R / T_j) * C_j
```

where `C` is the declared handler cost, `B` is the blocking term, and `T_j` and
`C_j` are the arrival period and cost of each higher-priority interrupt. The
iteration starts at `C + B`, rises monotonically, and stops when it settles or
when it passes the deadline. Passing the deadline ends it, because a response
time already beyond its deadline cannot be rescued by a further round.

This is Joseph and Pandya's formulation as extended by Audsley and others, and
it is standard rather than anything this project invents.

**The blocking term is the blackout window this project already computes.** An
interrupts-off region is a span in which the highest-priority work cannot run,
which is what `B` means. The two halves of the tool meet here: the composition
arithmetic produces the window, and the recurrence consumes it.

### Priority is numbered the way the hardware numbers it

A lower number is a higher priority, which is the convention of the interrupt
controllers on the parts this tool is aimed at. Adopting the literature's
opposite convention would read correctly in a textbook and wrongly against a
register an engineer transcribed from a datasheet.

### Utilisation comes first

`Σ C_j / T_j` is the first-order check, and it is cheaper than the recurrence
and easier to act on. It is carried in parts per million so that it stays exact
integer arithmetic. A set whose utilisation exceeds unity is unschedulable and is
reported as such without iterating.

### A verdict carries its margin

A response time inside its deadline by a hair and one inside by an order of
magnitude are different facts, and a binary verdict conceals which one the reader
has. Every schedulability verdict carries the slack alongside it.

## Consequences

- Good: the tool answers the question a real-time engineer asks first, and the
  blackout windows it already computes become the blocking term rather than a
  parallel result nobody could combine with anything.
- Good: an undeclared or guessed handler cost is visible in the verdict, so the
  analysis degrades honestly rather than silently.
- Bad: this changes what the tool claims to be. A calculator over declared waits
  is now also a schedulability analyser, and a reader will reasonably expect it
  to behave like one. The exclusions have to be stated more loudly, not less.
- Bad: the recurrence is a fixed-point iteration, so it needs a bound and a
  disposition for the case where it does not settle. Divergence is reported as
  unschedulable rather than as an internal failure, because a response time that
  grows without settling is exactly a system that cannot keep up.
- Neutral: interrupt nesting within a single priority level, and the release
  jitter that a sporadically periodic model would carry, stay outside. They are
  recorded with the other lacunae rather than assumed away.
