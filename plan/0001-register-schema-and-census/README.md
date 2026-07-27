# 0001 register-schema-and-census

The declaration everything else computes over, and the adapter that drafts it.

- **Who**: [Ines](../../cast/ines.md), who curates a register rather than
  authoring one from nothing, and [Tarn](../../cast/tarn.md), who edits and
  re-checks it in a loop.
- **What**: the specs under `spec/`.
- **Why**: [call/0002](../../call/0002-design-note-stays-out-of-repo.md) records
  where the design this milestone implements is held.

## The increment

A register file declares waits, windows, deadlines, compositions and
conversions. It is hand-authorable, diffable and reviewable, and it is the
artefact that gets committed alongside the image it describes.

A `Wait` carries a name, a budget, the counter type that budget is held in, a
measure, a cost per iteration, what happens on exhaustion, and the interrupt
state it runs under. The measure is first-class because a post-decrement that
wraps its counter and then tests against zero has a timeout that can never fire,
and the tool has to know which form it is looking at before it can say anything
about termination.

`census` walks an ELF and emits a skeleton register. It can supply the loops,
the back edges, the interrupts-off regions and the accesses that reach
peripheral space. It cannot supply a budget or a measure, so it emits those as
blanks carrying no provenance, and a human fills them in. Everything the adapter
does emit is marked `Extracted` with the file and symbol it came from.

## Acceptance

- A register round-trips through parse and print without losing a field.
- A skeleton emitted by `census` carries `Extracted` provenance on every value
  it supplies, and a blank wherever a human decision is required.
- A register with a blank in a required position is refused with a message
  naming the declaration, rather than defaulted.
- The freeze set the adapter reports is documented as a lower bound, because
  indirect calls through ops tables leave reachability unresolved.
