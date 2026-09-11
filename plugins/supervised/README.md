# supervised

Two models instead of one. One writes the code, a different one checks it.

Use it when being wrong would be expensive and wouldn't be obvious.

## Install

```sh
claude plugin marketplace add alonsorobots/claude-code-supervised && claude plugin install supervised@supervised-tools
```

Then:

```
/supervised <a plan file, or just tell it what you want>
```

Nothing to configure. That's it.

## What it actually does

**The checker never saw how the code got written.** So it can't nod along with
a bad idea it already agreed to. That's most of the value.

**Tests have to fail first.** It writes the test, runs it, shows you it's red,
*then* writes the code. A test that was never red might be testing nothing.

**Then it breaks your code on purpose.** If the test still passes, the test is
fake and it says so. This catches the thing that bites everyone: green tests
that were never going to go red.

**It keeps score on the checker.** Every complaint the reviewer makes gets
reproduced before anything is fixed, and logged as real or wrong. If the
reviewer turns out to be mostly wrong, it tells you to stop using it. It'll
happily fire itself.

**It learns your bugs.** Every real bug goes in a file in your repo. Next time,
the reviewer reads that first. Your fifth review is noticeably better than your
first, and you didn't do anything.

**It tells you when not to bother.** Renaming stuff? Small obvious fix? It says
"this isn't worth a review" and just does it. A review costs real money, so it
doesn't spend one on a typo.

**It notices when it forgets itself.** Long sessions get summarized and
instructions quietly fall out. It leaves a note in your plan file so it can spot
that it's happened and go re-read the rules.

## One honest thing

If both models come from the same company, they're wrong in similar ways. You're
not getting truly independent eyes — you're getting eyes that didn't watch the
code get written. Still useful, just less than it sounds.

There's [a study](https://arxiv.org/abs/2607.21656) where one model checking
another made things clearly better in one direction, and *worse than no check at
all* in the other. Which is exactly why this thing keeps score instead of
assuming it's helping.

## Tweaks (optional, skip this)

- Reviewer runs on Fable. Don't have it? It falls back and tells you. Or change
  `model:` in `agents/fable-reviewer.md`.
- Want to hand-write the bug list instead of letting it build up? See
  `REVIEW_BASE_RATES.template.md`.

MIT. Take it, change it.
