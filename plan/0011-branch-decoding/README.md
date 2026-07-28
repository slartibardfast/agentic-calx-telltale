# 0011 branch-decoding

The census reads an image's function symbols and stops there. Finding the
polling loops inside those functions is what would make it a census.

- **Who**: [Ines](../../cast/ines.md) is primary, with
  [Marek](../../cast/marek.md) behind her.
- **What**: the acceptance below, discharged by the tests and proofs in the software repo.
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
have to write, and decoding is one means among more than one.

## Boundaries come before branches

A back edge cannot be found without knowing where instructions start, and on the
parts this tool is aimed at that is the larger half of the work. The cost falls
into three tiers rather than two.

**Strided.** Every instruction is the same size, so a reader advances by a
constant and inspects. Cheapest by a wide margin.

**Length in a fixed prefix.** Sizes vary and the first unit says how long the
instruction is, so a reader advances without understanding it. Thumb-2 and the
compressed RISC-V forms are both here.

**Length in the operand form.** Sizes vary and the length depends on what the
instruction does, so a reader has to decode most of it before it can find the
next one. This is where writing a decoder stops being cheap, and it is where the
part in front of the operator sits.

The adapter reports which tier an image falls into, taken from the instruction
set its own header declares, so the cost is visible before anyone commits.

## The worked case, and why it is the hard one

An ARC EM part is the case to plan against, and it is worse than either of the
usual examples.

It carries four instruction widths rather than two, and the compact encodings
are the majority rather than a minority. Two width-changing mechanisms operate
independently: the compact forms, which play the part the compressed RISC-V
encodings do, and a long-immediate word appended to an instruction, which
produces the wider forms. Roughly a fifth of instructions carry one.

That second mechanism is what puts this in the third tier. Thumb-2 and the
compressed forms both let a reader take the length from a fixed prefix of the
first halfword. Here, whether a long-immediate word follows is a property of the
operand form, so the length function needs more of the instruction than either
of those cases.

The endianness answer misleads if taken flat. The image is little-endian, and a
long-immediate word is not a plain little-endian thirty-two bit value: it is two
little-endian halfwords with the high half first. Reading four bytes flat gets
the halves the wrong way round and finds nothing, which is a failure that looks
like absence rather than like an error.

Nor is a mature decoder a guarantee. On a stripped image of such a part, a
well-known reverse-engineering suite left roughly half its spans undecoded, and
the failures clustered on a major opcode with no entry in the published
instruction set at all. An encoding can be beyond the available tools rather
than merely expensive.

## Two ways to get boundaries, and the price of each

**Decode them.** Write the length function and the branch forms for one
encoding. Self-contained, runs on a bare image with no symbols and no toolchain,
and in the third tier a substantial piece of work that may not be finishable
against an incompletely published encoding.

**Delegate them.** Parse the listing a toolchain disassembler produces and take
boundaries from its address column, so the hard part falls to a decoder that
exists. Far cheaper, and it works today for encodings this project could not
decode soon.

The delegated route carries a dependency and a caveat, and the caveat is the
serious one. It needs a toolchain and a listing, so it cannot run on a bare
blob. The register's soundness then rests on that toolchain finding boundaries
correctly: a mis-lengthed instruction loses a back edge, and the freeze set is
wrong in a way nothing downstream can detect. That is the shape of dependency
this project exists to make visible, so it is now a declared limit and is stated
on every run rather than left in a note.

## The route, settled

Delegation, recorded in
[call/0012](../../call/0012-boundaries-are-delegated-to-the-listing.md). The
measured case decided it rather than the arithmetic of effort: on the part that
prompted this project a mature suite left roughly half its spans undecoded, so
writing a decoder would not merely have been expensive, it would not have
worked. A route that works is worth more than one that is self-contained.

The cast agreed for its own reasons. Marek already has the toolchain, so the
dependency costs him nothing he lacks. Ines gets something to curate this month.
Tarn loses a little, because an external process enters his loop, and is paid
back with a tool that is plain about a listing that is missing or unreadable
rather than one that quietly produces an empty draft.

## Where it stands

**Built.** `calx-telltale-census loops` reads a listing, groups instructions by
the function they sit in, recognises branches from a per-architecture mnemonic
set, and reports those whose target sits at or before them inside the same
function. On a real listing that is thousands of loops across hundreds of
functions, which is a freeze set rather than a symbol dump.

An architecture with no mnemonic set is declined by name. Guessing from the
shape of an operand would find loops in arithmetic that happens to reference an
address, and a false loop is worse than a missing one, because it is a
declaration a reader will act on.

Everything the adapter cannot determine stays blank. Whether a loop is a wait,
what budget it carries and how its counter moves are decisions for someone who
can read the source.

**Outstanding**: a loop with no backward branch is not a back edge, so a
zero-overhead loop instruction is missed, and the freeze set says it is a lower
bound for that reason among others. Coverage grows one architecture at a time,
and the gaps are visible rather than assumed away.

## Acceptance

- A loop is reported with the file and symbol containing it, and with blanks
  wherever a decision belongs to a human.
- An instruction the decoder does not recognise stops the analysis of that
  function and says so, rather than being skipped as though it were not a branch.
- Where boundaries are delegated, the run says so, and the limit that makes the
  freeze set only as sound as its source is reported with the rest.
- An image whose encoding the adapter cannot handle is declined by name, and the
  name comes from the image's own header rather than from a guess.
- The freeze set is reported as a lower bound, and the encodings covered are
  named among the limits stated on every run.
