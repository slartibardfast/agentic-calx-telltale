# Ines: the Human Operator

*The accountable firmware analyst who declares the register by hand.* (Project
persona, **primary**, embodied; see `call/0003`.)

**Modality: embodied and intermittent.** Ines perceives calx-telltale through
its README, its CLI help, and the numbers it prints. They read selectively, hold
a durable but lossy mental model of the image that survives weeks away, and
reason slowly, cross-checking against what they remember of the hardware. They
carry intent and accountability that live nowhere but in their head.

Ines arrives at this tool having already been wrong twice. Once an arithmetic
slip put a derived tolerance one count out, and the fault lay in how the
measurement was taken. Once a build script lifted a length out of a header with
a loose pattern match, and priced a walk three orders of magnitude too cheaply.
Both errors sat in arithmetic and plumbing, at a remove from the code under
study, and luck caught both. That history is why Ines wants verified arithmetic
in place of prose.

Their scenarios:

- declaring the waits, windows and deadlines for an image: the ELF adapter
  emits a skeleton, and Ines supplies the budgets and measures it left blank;
- reading a projection and judging whether it matches what they expect from the
  part, then treating any mismatch as the thing to explain;
- separating a number that was derived by sweep from one that was extracted from
  a header, and from one that was frankly guessed, while reading a single
  result.

- **Goals:** trust a projection enough to act on it; see which inputs a number
  rests on; find a worst case without having guessed where to look for it;
  reproduce a run from the pinned source.
- **Frustrations:** a derived number and a guess printed in the same typeface; a
  worst case that a sweep of the extremes reports as cheap; a tool that invents
  a unit conversion it has no evidence for; a result whose provenance was lost
  somewhere in the middle of its own derivation.
- **Works by:** curating a declaration rather than authoring it from nothing,
  running the CLI over it, and reading the witness alongside the cost. The
  judgement that a number "feels wrong" is theirs alone, and the tool exists to
  give that judgement something to bite on.

**Accommodations calx-telltale owes Ines:** a register format that is readable
and diffable by hand; a provenance label on every printed result; the witness
reported next to every maximum; the tool's exclusions stated on every run; a
README and CLI help that match the code.
