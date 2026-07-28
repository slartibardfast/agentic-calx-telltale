# 0005 deadlines-and-response-time

Armed deadlines first, and then the response-time question the whole enquiry
started from.

- **Who**: [Ines](../../cast/ines.md), who needs to know whether a projection
  breaches a deadline that is only armed for part of a run, and
  [Marek](../../cast/marek.md), who has to choose between deepening a buffer and
  shortening the window that overruns it.
- **What**: the acceptance below, discharged by the tests and proofs in the software repo.
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

The sharper form of the question is buffer overflow rather than latency. A
handler that drops entries when its ring fills and only logs the fact has a
failure that arrival rate and buffer depth predict, and that a latency figure
does not.

An interrupt therefore declares an arrival model, a deadline, and the depth of
the buffer behind it. Two verdicts follow. Latency asks whether the blackout
outlasts the deadline. Overrun asks whether more arrivals land during the
blackout than the buffer holds, where a window of length `b` admits at most
`floor(b / gap) + 1` of them, one at the instant it opens and the rest no faster
than the shortest declared gap.

Both verdicts can be withheld, and a withheld verdict is a verdict rather than
an error ([call/0008](../../call/0008-interrupts-are-declared-and-answerability-is-a-verdict.md)).
A blackout counted in loop passes cannot be compared against a deadline in
nanoseconds, and a run reports how many comparisons it withheld so that an
exclusion never reads as a clean sweep.

An interrupt also declares how long its handler runs, what priority it holds,
and what a dropped arrival costs. That admits the standard fixed-priority
analysis over the declared set
([call/0010](../../call/0010-response-time-over-a-priority-ordered-set.md)):
utilisation as the first-order check, then the response-time recurrence, whose
blocking term is the blackout window this project already computes. A verdict
carries its margin, because holding by a hair and holding comfortably are
different facts.

The handler cost is declared rather than derived. This tool computes no
worst-case execution time and it does not pretend the quantity is absent either,
so a cost somebody guessed makes every verdict resting on it a guess.

**Built**: both window verdicts, the arrival count, utilisation, the
response-time recurrence, the margin, the withheld cases, and the sweep that
reports them. The exclusions are now enumerated in the crate and stated on every
run, which the founding note asked for and nothing previously did.

**Proof coverage is partial, and deliberately so.** Harnesses hold the parts
that carry the design: a window with no path to a time is never resolved either
way, a verdict never outranks the weakest declaration it rests on, and priority
is a strict order, which is what lets the recurrence partition the set. The
recurrence itself walks a heap-allocated set and is covered by tests, on the
same reasoning as [call/0009](../../call/0009-multiplication-is-proved-at-the-boundaries.md).

Arming, jitter and nesting have since landed, and each removed a limit the tool
had been declaring rather than modelling.

A deadline now carries the span it is in force over. A window outside that span
gets its own verdict rather than a pass, because an unbounded stretch is worse
than a bounded one and reporting it as met would say the opposite of what is
true. It counts with the withheld rather than the passes.

An arrival may declare how late a release can be. A source that is periodic in
the long run still bunches, and a burst puts more arrivals into a window than
even spacing would, which can overrun a ring the even case fits.

A handler may declare that it re-enables interrupts inside itself, and the
interference set then widens to its own priority level. A flat model misses that
entirely.

**Outstanding**: the arrival adapter that would populate rates from something
other than a human's declaration.

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
- A common-mode excursion. A comparison between clocks off one root is exact,
  because the root's error moves both and cancels. What stays outside the model
  is the root leaving its own declared tolerance, which would move every clock
  beneath it at once.
- Nested budgets. Each wait is priced in ignorance of what its callers have
  already spent.
