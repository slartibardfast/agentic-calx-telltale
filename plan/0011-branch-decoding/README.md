# 0011 branch-decoding

The census reads an image's function symbols and stops there. Finding the
polling loops inside those functions is what would make it a census.

- **Who**: [Ines](../../cast/ines.md) is primary, with
  [Marek](../../cast/marek.md) behind her.
- **What**: the specs under `spec/`.
- **Why**: every milestone so far assumes a register exists, and this is the one
  that would produce a real one.

## What the cast says

Ines "works by curating a declaration rather than authoring it from nothing",
and today there is very little to curate: the adapter hands her a list of
function names. Her persona describes a mode the tool does not yet support, and
this milestone is the one that would. She is primary because the gap between
what her persona says she does and what the tool lets her do is widest here.

Marek's entry is "a register large enough that keeping it current becomes its
own job". A register drafted from the image tracks the image; one authored by
hand drifts from it, and a drifted register is a review artefact nobody trusts.
His other entry, "a tool that reports a hazard on a part it cannot reach", is a
warning about how this milestone can go wrong rather than an argument for it.

Neither persona asks for a disassembler. They ask for a register they did not
have to write, and decoding is the means.

## The decision this milestone waits on

Finding back edges needs branch targets, and branch targets need decoding.
[call/0011](../../call/0011-the-adapter-sits-beside-the-core.md) keeps that out
of the core while leaving the adapter free to take it, so what remains open is
which decoder rather than whether to have one.

A full disassembler is more than this needs. Back edges want branch instructions
and their displacements, which for one architecture is a bounded subset of the
encoding rather than the whole of it. That is the shape to aim for.

Which architecture comes first is an operator decision, because it depends on
the part in front of them and nothing in this project settles it. Whichever is
chosen, the others are uncovered, and the freeze set becomes per-architecture,
which has to reach the limits the tool states on every run rather than being
left for a reader to infer.

## The increment

For the chosen architecture, decode branch instructions inside a function's
extent, take the ones whose target precedes them, and report the resulting loops
as candidate waits with the citation the symbol already carries.

Everything the adapter cannot determine stays blank. A budget, a measure and a
counter width are still human decisions, and this milestone does not change that.

## Acceptance

- A loop is reported with the file and symbol containing it, and with blanks
  wherever a decision belongs to a human.
- An instruction the decoder does not recognise stops the analysis of that
  function and says so, rather than being skipped as though it were not a branch.
- An image of an architecture the adapter does not decode is declined by name.
- The freeze set is reported as a lower bound, and the architectures covered are
  named among the limits stated on every run.
