# 0008 census-adapter

A register authored entirely by hand is a register nobody keeps current. The
adapter drafts one from the image, leaving blanks where a human has to decide.

- **Who**: [Ines](../../cast/ines.md), who curates rather than authors, and
  [Marek](../../cast/marek.md), for whom a register large enough to maintain by
  hand becomes its own job and therefore stops being maintained.
- **What**: the specs under `spec/`.
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

## Acceptance

- A skeleton carries `extracted` provenance with a citation on every value it
  supplies, and a blank wherever a human decision is required.
- A blank reaches the reader as a blank rather than as a default, so nothing
  the adapter could not determine is silently invented.
- The freeze set is reported as a lower bound wherever it is reported.
- The core's dependency list stays empty and its offline build keeps working.
