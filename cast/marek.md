# Marek: the Firmware Engineer

*The person who owns the code the register describes.* (Project persona,
**primary**, embodied; see `call/0003`.)

**Modality: embodied and pressed.** Marek perceives this project through
whatever it says about firmware they are responsible for. They did not come
looking for a timing tool. They arrived because a verdict landed on work of
theirs, and they have a release to make.

Marek is not the operator who curates the register. [Ines](ines.md) reads a
projection and asks whether to trust it; Marek reads the same projection and
asks what to change, and whether it is worth changing before the release. The
two want different things from the same output, which is why the tool owes them
different accommodations.

Marek also wrote some of what the register describes. A wait whose timeout can
never fire is theirs, and a verdict saying so arrives as an accusation unless it
carries enough to check. That makes credibility the whole game: a finding Marek
cannot reproduce or locate is a finding they will learn to route around, and one
bad verdict costs the tool more standing than ten good ones earn it.

Their scenarios:

- receiving a verdict that a declared timeout can never fire, and needing it to
  name the declaration and the code it was taken from before they will act;
- being told a ring overruns during a blackout, and having to choose between
  deepening the buffer and shortening the window;
- reviewing a register against firmware they changed, and seeing which
  declarations went stale with the change;
- being asked for an arrival rate and a buffer depth that nobody ever wrote
  down, on a part where neither is documented.

- **Goals:** find out what to change and how much it matters; keep a verdict
  reproducible on their own machine; get the timing story into review alongside
  the code rather than after it; stop being surprised at integration.
- **Frustrations:** a bound that names no declaration and no symbol, so there is
  nothing to open; a report hedged into uselessness because every input was
  guessed; a register large enough that keeping it current becomes its own job;
  a tool that reports a hazard on a part it cannot reach.
- **Works by:** reading a verdict against code they already know, spot-checking
  the declaration it rests on, and either fixing the firmware or fixing the
  declaration. They are the one who can tell which of the two was wrong.

**Accommodations calx-telltale owes Marek:** a verdict that names the
declaration it rests on, and a declaration that names the file and symbol it was
extracted from; a census that drafts the register so it is curated rather than
authored; `diff` across builds, so a change that moves a bound surfaces in
review; the consequence of a drop recorded, so an overrun says what is lost
rather than only that something was; exit codes and output that sit in a build
pipeline without a human reading them.
