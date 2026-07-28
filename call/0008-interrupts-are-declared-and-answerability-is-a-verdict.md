# Interrupts are declared, and whether a window can be judged is itself a verdict

- Status: accepted; the priority exclusion superseded by [call/0010](0010-response-time-over-a-priority-ordered-set.md)
- Scope: software
- Date: 2026-07-27

## Context and Problem Statement

The founding note records interrupt response time as the least developed part of
the design, and as the thing the whole enquiry started from. It sits outside the
register's stated scope: the register measures how long interrupts are off, and
says nothing about whether any interrupt misses anything as a result.

That gap has a shape now. A blackout window's cost can be compared against an
interrupt's deadline only if the cost can reach a unit the deadline is expressed
in. A window whose delay is a bare spin against a clock that is being
reconfigured has a cost in iterations, and by [call/0006](0006-sources-are-declared-and-delays-name-them.md)
no conversion to time exists for it. So the source and delay model already
decides which windows the response-time question can be asked of, and for some
windows the honest answer is that it cannot be asked at all.

That answer is worth more than a number. A tool that silently assumed a rate to
produce a microsecond figure for such a window would be doing the exact thing
this project exists to stop.

## Decision

### Interrupts are declared entities

An `Interrupt` declares:

- an **arrival model**, either periodic from a named source or bounded by a
  minimum interarrival time. Either way the rate is a `Quantity`, so a tolerance
  and a provenance travel with it under
  [call/0007](0007-rates-are-exact-rationals.md);
- a **deadline**, the latency it can absorb before it has missed;
- a **buffer depth**, how many arrivals can queue before one is lost;
- the **consequence of a drop**, recorded so a verdict can say what was lost
  rather than only that something was.

### Two verdicts, and the sharper one is overrun

**Latency**: does the blackout exceed the deadline. This is the question people
ask first.

**Overrun**: do the arrivals during the blackout exceed the buffer depth. This
is the question that actually bites, and it is the one the enquiry started from,
because a handler that drops entries when its ring fills and only logs the fact
fails without ever missing a latency figure anybody was watching. The arrival
count over a blackout is the blackout's upper bound divided by the shortest
interarrival time, which is arithmetic this project already has.

### The word is overrun, and deliberately not occupancy

An earlier draft called this occupancy. That word is taken, and taken by the one
project a reader here is most likely to have open alongside this one.

calx-mill uses occupancy for the saturation of a substrate's concurrency
dimension. It is a percentage bounded to a hundred, it is steady-state, and it
asks how much of the available parallelism is resident. The question here is a
burst against a
fixed depth, which is transient, counted rather than proportioned, and answered
with a verdict rather than a ratio. The two share a word and nothing else. Since
this project names calx-mill as its design sibling and follows its command
idiom, a reader crossing between them would have been walked straight into the
confusion rather than warned off it.

The founding note's own word was overflow, and that is unavailable for a
different reason: this crate already spends it on arithmetic that leaves the
width. Reusing it would put the collision inside a single file rather than
across two repositories, which is worse.

So the verdict is overrun. It is ordinary vocabulary in the domain the operator
already works in, it names a failure and therefore reads in parallel with a
missed deadline, and nothing else here claims it. The quantity the verdict rests
on is the backlog.

### Unanswerable is a verdict, not an error

Where the blackout's cost cannot reach the unit the deadline is written in, the
tool reports the comparison as unanswerable and names what is missing, which is
either a conversion or a clock that is invalid across the window. The verdict is
withheld rather than resolved either way, and nothing is guessed to fill it. A
run reports how many comparisons it withheld, so an exclusion never reads as a
clean sweep.

### The response-time milestone moves ahead of termination

`plan/PLAN.md` is the ordering authority, and this work moves ahead of
termination-and-counter-fit in it. The dependencies allow it: response time needs
units, provenance and composition, and it does not need the loop contracts that
the termination work waits on. Nothing forces it to sit last except the order it
was first written in, and the operator has since called it the meat of the
project.

Milestone numbers are identity rather than sort keys, so the folders keep the
names they were given and only the index moves.

## Consequences

- Good: the piece the enquiry started from stops being a postscript, and the
  source model earns its keep twice over, once for converting a bound and once
  for deciding whether a bound can be compared at all.
- Good: overrun exposes a failure mode that latency analysis misses entirely.
- Bad: a register that wants response-time verdicts has to declare arrival rates
  and buffer depths that nobody currently writes down, and most of those will
  start out `Assumed`. The tool will say so on every verdict, which is the point
  and will still be unwelcome.
- Neutral: interrupt priority, preemption and nested handlers stay outside the
  model. A declared interrupt is judged against blackout windows alone. **This
  no longer holds.** Consulting the cast against a real-time workload showed the
  exclusion removed the middle of the subject rather than an edge of it, and
  [call/0010](0010-response-time-over-a-priority-ordered-set.md) takes priority
  and preemption into the model. Nesting within a single priority level stays
  out, and is recorded with the other limits the tool states on every run.
