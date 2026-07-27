# 0005 deadlines-and-response-time

Armed deadlines first, and then the response-time question the whole enquiry
started from.

- **Who**: [Ines](../../cast/ines.md), who needs to know whether a projection
  breaches a deadline that is only armed for part of a run.
- **What**: the specs under `spec/`.
- **Why**: the interval a deadline is armed over changes the verdict, and prose
  has been carrying that distinction so far.

## Deadlines

A `Deadline` carries a budget and the interval it is armed over. Deadlines are
first-class because a watchdog is typically enabled part-way through a boot,
which leaves everything before that point with no deadline at all. That is a
different situation from having one, and a worse one, and the tool should be
able to say so rather than leave it to a footnote.

`deadline` compares each projection against the deadlines armed over the
interval that projection covers.

## Response time

The register measures how long interrupts are off. It says nothing about whether
any interrupt misses a deadline, which is a different calculation needing
per-source arrival rates and per-source deadlines.

The sharper form of the question is buffer overflow rather than latency. An ISR
that drops entries when its ring fills and only logs the fact has a failure that
arrival rate and buffer depth predict, and that a latency figure does not. This
is the least developed part of the design, and it is deliberately last.

## Acceptance

- A projection covering an interval where no deadline is armed reports that
  fact, rather than reporting a pass.
- A projection breaching a deadline armed over its interval fails, and the
  failure names both the deadline and the witness.
- The response-time work begins from a written model of arrival rate, buffer
  depth, and the consequence of a drop, agreed with the operator before any code.

## Out of scope, stated on every run

Recorded here so they cannot be mistaken for covered:

- Execute-in-place instruction fetch over serial flash. A code span does not
  convert to a time on such a part, so a byte count stays a byte count.
- Hangs that are not loops. A take on a semaphore nobody gives is invisible to a
  back-edge census and still hangs a boot.
- Unresolved indirect calls, which make any freeze set a lower bound.
- Windows that open in one stack frame and close in another.
- Correlated failure. Each wait's latency is modelled independently, and one
  unclocked block makes every register behind it misbehave together.
- Nested budgets. Each wait is priced in ignorance of what its callers have
  already spent.
