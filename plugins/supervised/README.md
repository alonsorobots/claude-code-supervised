# supervised

**Coding agents grade their own homework.**

The agent writes the code. Then it writes the test. Then it runs the test, the
test passes, and it tells you it's done. But the same model wrote both — so if
it misunderstood something, the test has the same misunderstanding baked in. It
passes *because* of the bug.

That's why you get green checkmarks on code that breaks a week later.

**This makes a second model check the work.** One that never saw how the code
got written, so it can't agree with a mistake it already made.

Use it when being wrong would be expensive and wouldn't be obvious.

---

## How to install it

Paste this into your terminal:

```sh
claude plugin marketplace add alonsorobots/claude-code-supervised && claude plugin install supervised@supervised-tools
```

That's the whole setup. Nothing to configure.

## How to use it

In Claude Code, type:

```
/supervised add rate limiting to the API
```

Or point it at a plan file you already have:

```
/supervised ~/.claude/plans/my-plan.md
```

Then leave it alone. It works through the job in phases and checks itself at
each one.

---

## What you get

**A second model checks the work.** It reads the actual diff and runs the actual
tests. It's not allowed to review off a summary, which is where most AI review
goes wrong — it just agrees with whatever it's told.

**Tests have to fail before they pass.** It writes the test, runs it, shows you
it's red, *then* writes the code. A test that was never red might be testing
nothing at all.

**It breaks your code on purpose.** After the test goes green, it goes back and
sabotages the thing the test is supposed to be guarding. If the test still
passes, the test is fake and you get told. This is the one that catches the
"grading its own homework" problem.

**It keeps score on the reviewer.** Every complaint gets reproduced before
anything is changed, and logged as real or wrong. If the reviewer turns out to
be mostly wrong, it tells you to stop using it. It will fire itself.

**It learns your bugs.** Every real bug goes into a file in your repo. Next
review reads that first. Your fifth review is noticeably sharper than your
first and you did nothing to make that happen.

**It tells you when not to bother.** Renaming a variable? Obvious one-line fix?
It says "not worth a review" and just does it. Reviews cost real money.

**It notices when it's forgotten the rules.** Long sessions get summarized and
instructions quietly drop out. It leaves itself a note so it can catch that and
go re-read them.

---

## One honest thing

If both models come from the same company they're wrong in similar ways. You're
not getting truly independent eyes — you're getting eyes that didn't watch the
code get written. Still useful. Just less than it sounds.

There's [a study](https://arxiv.org/abs/2607.21656) where one model checking
another made things clearly better in one direction, and *worse than no check at
all* in the other. That's exactly why this keeps score instead of assuming it's
helping.

## Optional tweaks

- The reviewer runs on Fable. No access? It falls back and tells you. Or change
  `model:` in `agents/fable-reviewer.md`.
- Want to write the bug list yourself instead of letting it build up? See
  `REVIEW_BASE_RATES.template.md`.

MIT. Take it, change it.
