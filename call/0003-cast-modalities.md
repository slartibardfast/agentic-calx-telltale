# calx-telltale owes an accommodation to each modality

- Status: accepted
- Scope: software
- Date: 2026-07-27

## Context and Problem Statement

calx-telltale is run by a human analyst reading numbers by eye, and by an
agentic model driving the CLI in a loop. The two perceive the tool through
different channels and fail in different ways.

The failure that matters is specific to this tool. A provenance label carried
only in the printed report is invisible to an agent parsing structured output,
and an agent will carry an `Assumed` value into a deadline comparison and report
the verdict with no hedge. A provenance that reaches only one of the two
surfaces defeats the property the tool is named for.

## Decision

The cast splits on modality, and both personas are primary. An interface
designed for one cannot satisfy the other (Cooper 2004), so each is owed its own
accommodation:

- [Ines](../cast/ines.md), embodied and intermittent, is owed a readable
  register format, a provenance label on every printed result, the witness
  reported next to every maximum, and the tool's exclusions stated on every run.
- [Tarn](../cast/tarn.md), textual and ephemeral, is owed a stable
  machine-readable output shape carrying provenance as a field, the witness
  present in that structured output, exit codes that distinguish a failed
  obligation from a malformed register, and a line-oriented register format.
- [Marek](../cast/marek.md), embodied and pressed, is owed a verdict that names
  the declaration it rests on and a declaration that names the code it came
  from, a census that drafts the register rather than leaving it to be authored,
  and a comparison across builds so a moved bound reaches review.

Marek joined the cast on a second axis. Modality was the first split, and role
is the second, because Marek shares Ines's modality and wants the opposite thing
from the same output: Ines asks whether a projection can be trusted, and Marek
asks what to change. A verdict can satisfy the first and be useless to the
second by being trustworthy and attached to nothing anyone can open.

The binding rule across both: provenance and the witness are part of the data,
not part of the prose. Any surface that reports a cost reports the provenance
that cost carries and the input that attains it.

## Consequences

- Good: provenance monotonicity is observable from either surface, so the property the name promises
  can be checked by the persona that reads it.
- Good: the structured output shape becomes a compatibility commitment, which is
  what makes an agent's loop safe across releases.
- Bad: two surfaces cost more to build and more to hold in step, and every
  result type has to be expressible in both.
- Neutral: this mirrors the modality split calx-mill settled on, so the two
  siblings present the same way.
