# 0008 census-adapter

A register authored entirely by hand is a register nobody keeps current. The
adapter drafts one from the image and leaves a blank wherever a human has to
decide.

- **Who**: [Ines](../../cast/ines.md), who curates rather than authors, and
  [Marek](../../cast/marek.md), for whom a register large enough to maintain by
  hand becomes its own job and therefore stops being maintained.
- **What**: the acceptance below, discharged by the tests and proofs in the software repo.
- **Why**: every other milestone assumes a register exists. This is where one
  comes from.

## The decision this milestone waits on

Walking an image for loops, back edges and interrupts-off regions needs a reader
for the object format, and that would be this crate's first dependency. The
dependency list being empty is not an aesthetic here: it is why the release
builds offline inside the pinned container with no vendored bundle, which no
other component in this host manages.

So the adapter goes beside the core rather than inside it, and the placement
wants recording before any code. The verified arithmetic keeps its empty
dependency list and its offline build; the adapter takes its reader and lives
with a vendored bundle like everything else. A `call/` decision settles this
first.

## The increment

The adapter emits a skeleton register. It can supply the loops, the back edges,
the interrupts-off regions and the accesses that reach peripheral space. It
cannot supply a budget, a measure or a counter width, so it emits those as
blanks for a human, and everything it does emit carries a citation naming the
file and symbol it came from, which is what 0006 makes expressible.

The freeze set it reports is a lower bound rather than a census. Calls through
ops tables and device interfaces leave reachability unresolved, and that is
already recorded among the limits the tool states on every run.

## Consulting the cast

- **Ines** stops authoring and starts curating, which is the working mode her
  persona describes and the tool has not yet supported.
- **Marek** gets a register that tracks the image rather than drifting from it,
  which is the difference between a review artefact and a stale document.
- **Tarn** gains the loop this milestone was always for: run the adapter,
  fill what can be filled, check, repeat until the obligations discharge.

## Where it stands

**Built**: the adapter, as a workspace member beside the core. It reads an
image's function symbols and drafts a register from them, with a citation on
every entry and a `?` wherever a decision belongs to a human.

The object-format reader is written here rather than taken as a dependency,
which is [call/0011](../../call/0011-the-adapter-sits-beside-the-core.md)
applied at the line that record drew: parsing an object file is bounded work
against a published layout. Every field is read with a bounds check, because the
input is a binary somebody else built and a census that panicked on a malformed
image would be worse than one that declined. A file that is not an image, is the
wrong width, or is truncated is declined by name.

The core's dependency list is still empty and the canonical artifact is still
built with `-p calx-telltale`, so the offline release build is untouched.

**Outstanding, and stated on every draft**: these are candidates rather than
waits, and the draft now says so by construction rather than only in prose.
Every candidate line is commented out. A wait is a polling loop, a function
symbol is not, and emitting a declaration per symbol claimed a kind the adapter
had not established, once for every function in the image. A reader uncomments a
line once they have looked and found a loop worth declaring.

Finding those loops is what would make this a census, and it needs the
instruction boundaries [0011](../0011-branch-decoding/README.md) is about. What
the adapter misses without them it misses silently, which is why the header says
so.

## Acceptance

- A skeleton carries `extracted` provenance with a citation on every value it
  supplies, and a blank wherever a human decision is required.
- A blank reaches the reader as a blank rather than as a default, so nothing
  the adapter could not determine is silently invented.
- The freeze set is reported as a lower bound wherever it is reported.
- The core's dependency list stays empty and its offline build keeps working.
