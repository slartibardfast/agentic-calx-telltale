# Boundaries are delegated to a toolchain listing

- Status: accepted
- Scope: software
- Date: 2026-07-28

## Context and Problem Statement

[0011](../plan/0011-branch-decoding/README.md) leaves two routes open and prices
both. Boundaries can be decoded here, or taken from the address column of a
listing a toolchain disassembler already produced. The milestone waits on which,
and on which encoding.

## What the cast says

**Marek** already has the toolchain. He builds the firmware, so a route that
needs a disassembler costs him nothing he does not already have, and his
recorded frustration is a register that becomes its own job to maintain rather
than a missing feature. His other entry, "a tool that reports a hazard on a part
it cannot reach", argues against the decoding route rather than for it: a
half-finished decoder that reports loops it half-found is exactly that.

**Ines** works by curating rather than authoring, and today has almost nothing
to curate. Her interest is in whichever route produces something this month.

**Tarn** loses something and has to be paid back. Delegation puts an external
process in the middle of his loop, so the tool has to be plain about a listing
that is missing, truncated, or from a different binary, rather than quietly
producing an empty draft that reads like a clean result.

## Decision

Boundaries are delegated. The adapter parses a disassembly listing and takes
instruction addresses from its address column, so the encoding is somebody
else's problem.

The measured case settles it rather than the arithmetic of effort. On the part
in front of the operator the length of an instruction depends on its operand
form, which is the tier where a decoder stops being cheap. A mature
reverse-engineering suite left roughly half its spans undecoded on a stripped
image of that part, and the failures fell on a major opcode with no entry in the
published instruction set. Writing a decoder would not merely have been
expensive; on that binary it would not have worked either. A route that works is
worth more than a route that is self-contained.

**Branches are recognised per architecture and nowhere else.** The listing names
the architecture in its own header, and the adapter carries a set of branch
mnemonics for the ones it knows. An architecture with no set is declined by
name. Guessing from the shape of an operand would find loops in arithmetic that
happens to reference an address, and a false loop is worse than a missing one:
it is a declaration a reader will act on.

**A back edge is a branch whose target sits at or before it, inside the same
function.** That is what makes it a loop rather than a jump onward.

## Consequences

- Good: it works today, on the part that prompted the project, and on any other
  the toolchain can disassemble.
- Good: the freeze set stops being empty, so Ines has something to curate and
  the adapter stops offering function symbols as though they were candidates.
- Bad: it needs a toolchain and a listing, so it cannot run on a bare blob. That
  is the case the decoding route would have served, and it is the case where a
  decoder was measured failing anyway.
- Bad: the register's soundness rests on the toolchain finding boundaries
  correctly. A mis-lengthed instruction loses a back edge and the freeze set is
  wrong in a way nothing downstream detects. This is already a declared limit and
  is stated on every run.
- Neutral: coverage grows one architecture at a time, since an architecture
  whose branch mnemonics are absent is declined by name. The gaps stay visible.
