# 0006 declarations-cite-their-source

A verdict that names a wait number tells the person who has to act almost
nothing. This milestone makes a declaration carry where it came from, so a
finding can point at code.

- **Who**: [Marek](../../cast/marek.md), primary here. His written accommodation
  is a declaration that names the file and symbol it was extracted from, and the
  register format carries no such field, so the accommodation is unmet.
- **What**: the specs under `spec/`.
- **Why**: [call/0003](../../call/0003-cast-modalities.md) sets what each
  modality is owed, and this is the part of Marek's share the schema never grew.

## The increment

`Provenance` says how a value was arrived at. It does not say where. That was a
deliberate split: citations are free text, and
[call/0006](../../call/0006-sources-are-declared-and-delays-name-them.md) keeps
free text outside the proof boundary so a proof spends its budget on the
arithmetic. The split is right and the consequence was overlooked, because the
register never grew the fields the reporting layer was supposed to hold.

So a declaration gains an optional citation: the file it was read from and the
symbol inside it. Optional because a value that was swept or guessed has no
symbol to name, and requiring one would push authors toward inventing them.

A citation is reported wherever the declaration it belongs to reaches a verdict.
Marek reads a failure and opens the named site; without that the verdict is a
number attached to an identifier only the register explains.

## Consulting the cast

- **Marek** gains the thing that makes a verdict actionable. He is the reason
  this milestone is first.
- **Ines** gains a register that survives its author. She curates declarations
  over weeks, and a citation is what lets her check one she no longer remembers.
- **Tarn** gains a field, and the structured surface carries it like any other.

## Where it stands

**Built.** A wait may carry `file=` and `symbol=`, and both report through the
prose surface and the structured one. The grammar the tool prints documents
them, so an agent learns the fields from the binary.

Citations sit beside the declarations rather than inside them. The verified
types stay free of text, which keeps the proof boundary where
[call/0006](../../call/0006-sources-are-declared-and-delays-name-them.md) put
it, and the reporting layer has what it needs.

A `?` in either field reaches the reader as absent rather than as a literal
question mark, so a census that could not determine a site says so.

A clock carries one too. Its frequency is the value most worth tracing, because
everything timed by that clock rests on it, and a report names the site
alongside the nominal rate and the tolerance.

## Acceptance

- A declaration may carry a file and a symbol, and a verdict resting on it
  reports both, in the prose surface and in the structured one.
- A declaration carrying no citation still parses, and a verdict resting on it
  states that the citation is absent.
- A citation is never invented. Nothing in the tool derives one from a value
  that carries none.
- The proof boundary is unchanged. Nothing under `cargo kani` reads a
  citation, because a citation is not part of the arithmetic.
