# Review base rates — <your repo>

Copy this to `.claude/REVIEW_BASE_RATES.md` in your repo and fill it in. The
reviewer reads it before anything else and it outranks the generic classes in
the agent file.

**This is the part that makes supervised review worth paying for.** A reviewer
with a generic checklist finds generic things. A reviewer that knows your repo
produced four "controls that could not fail" in two months goes looking for the
fifth.

## How to fill it in

Do not invent classes. Harvest them. After each real defect that got past a
green test suite, add one line here:

    CLASS — one-sentence shape. <count> sightings.
      <the concrete instance, with the number or command that exposed it>
      **The question to ask:** <what a reviewer should ask to catch the next one>

A class earns its place at 2 sightings. One sighting is an anecdote; write it
down anyway, under `## Singles`, and promote it when it recurs.

## Seed (delete what does not apply)

Most codebases eventually produce these. Keep the ones you have actually seen,
with YOUR instances — a class with no instance from your repo is decoration.

- **A control that cannot fail** — asserts the delivery of an input rather than
  the effect at the layer the property lives.
- **A fixture that encodes the bug** — the test passes only while the defect
  exists.
- **Shipped green and unreachable** — the test mirrors the caller instead of
  driving it, so dead code looks covered.
- **A stale baseline** — a remembered test count hides a real regression.
- **A measurement that cannot support its claim** — a signal that moves for two
  reasons, used to name one of them.
- **A confounded run** — a shared or dirty checkout means the green was not the
  commit under review.

## Singles

(one-off defects not yet a class)
