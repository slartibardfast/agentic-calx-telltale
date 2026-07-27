# Interrupts are declared, and whether a window can be judged is itself a verdict

- Status: accepted
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

### Two verdicts, and the sharper one is occupancy

**Latency**: does the blackout exceed the deadline. This is the question people
ask first.

**Occupancy**: do the arrivals during the blackout exceed the buffer depth. This
is the question that actually bites, and it is the one the enquiry started from,
because a handler that drops entries when its ring fills and only logs the fact
fails without ever missing a latency figure anybody was watching. The arrival
count over a blackout is the blackout's upper bound divided by the shortest
interarrival time, which is arithmetic this project already has.

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
- Good: occupancy exposes a failure mode that latency analysis misses entirely.
- Bad: a register that wants response-time verdicts has to declare arrival rates
  and buffer depths that nobody currently writes down, and most of those will
  start out `Assumed`. The tool will say so on every verdict, which is the point
  and will still be unwelcome.
- Neutral: interrupt priority, preemption and nested handlers stay outside the
  model. A declared interrupt is judged against blackout windows alone.
