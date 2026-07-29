# 0010 arrivals-from-the-clock-tree

An interrupt's arrival period is declared by hand today, even where the register
already declares the clock that produces it.

- **Who**: [Marek](../../cast/marek.md), primary here.
- **What**: the acceptance below, discharged by the tests and proofs in the software repo.
- **Why**: the derivation machinery this needs already exists, and it is aimed
  at the frustration the cast records most sharply.

## What the cast says

Marek's recorded frustrations name this twice over. The first is "a report
hedged into uselessness because every input was guessed". A response-time
verdict rests on arrival rates, and every rate a human types is one more chance
for the whole verdict to come back `Assumed`. The second is "a register large
enough that keeping it current becomes its own job", which is what a register
full of restated numbers becomes: a timer's rate is already in the file, and
writing its period again beside every interrupt it drives is duplication that
drifts.

Marek is primary because he is the one who pays for both. He arrives under
release pressure, and a verdict he cannot trust is one he routes around.

Ines gains from the same change for her own reason. Her working mode is
"curating a declaration rather than authoring it from nothing", and a derived
period is one fewer thing to author. Tarn gains a register with fewer places for
a value to be wrong.

## The increment

A periodic interrupt already names the clock it is driven by. Where it does, it
declares the divisor rather than the period, and the period follows exactly from
the clock's rate.

This is the derivation that already runs for clocks themselves, applied one
level out. The rate is exact, so the period is exact. The clock's provenance
reaches the period without anything further being wired, so a guessed frequency
makes the arrival rate a guess and says so, which is the behaviour Marek needs
and cannot get from a number typed beside it.

Where two interrupts derive from one clock, they are correlated in the sense the
source table already computes, so a comparison between them carries no tolerance
at all.

## Where it stands

**Not started.** The derivation this needs already runs for clocks themselves;
what is missing is applying it one level out, to an interrupt that names the
clock driving it.

## Acceptance

- An interrupt declaring a clock and a divisor needs no period, and the period
  derived matches what the period field would have said.
- The clock's provenance reaches the derived period, and a guessed frequency
  makes every verdict resting on that arrival a guess.
- A declared period that disagrees with the clock and divisor is refused rather
  than silently preferred, in the way a derived clock rate already is.
- An interrupt whose arrival comes from outside the register keeps its declared
  period, since a line fed by an external peer has nothing here to derive from.
