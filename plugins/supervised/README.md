# supervised

A working method for agentic coding where **being confidently wrong is
expensive**: one model plans and reviews, another executes, gates are written
red before they are implemented and mutation-checked afterwards, and every
review finding is adjudicated so you can tell whether the review is earning its
cost.

## Install

```
/plugin marketplace add alonsorobots/claude-code-supervised
/plugin install supervised@supervised-tools
```

Then, in a session:

```
/supervised ~/.claude/plans/my-plan.md     # or: /supervised <a goal, to plan first>
```

## What you get

- `/supervised` — the method: phased execution, gate-first, mutation-checked,
  with an explicit "when this is NOT worth paying for" table.
- `fable-reviewer` — an adversarial review agent that runs on a different model,
  reads diffs and RUNS commands, and is forbidden from reviewing off a summary.

## Two things to customise before it is worth much

**1. The base rates — this is the load-bearing part.**
Copy `REVIEW_BASE_RATES.template.md` to `.claude/REVIEW_BASE_RATES.md` in your
repo and fill it with defects *your* codebase has actually produced. The
reviewer reads it first and it outranks the generic classes. Without it you get
a competent generic review; with it you get a review aimed at your real failure
history. The shipped classes are deliberately generic and are kept
only because they teach the shape.

**2. The reviewer model.**
`agents/fable-reviewer.md` has `model: fable` in its frontmatter. Change it to
whatever model you want reviewing, and make sure your account has access to it.

## The honest caveat about "independence"

If the executor and the reviewer come from the same lab (e.g. Opus executing,
Fable reviewing — both Anthropic), you are NOT getting decorrelated blind spots.
Shared pretraining lineage and post-training mean the two models fail in
similar ways. What you still get, and it is worth real money:

1. **Fresh context** — the reviewer never saw the reasoning that produced the
   code, so it cannot inherit a wrong premise by having already agreed to it.
2. **No authoring commitment** — it is not defending work it wrote.
3. **A different model** — decorrelates *some* errors. A bonus, not the thesis.

A 116-task controlled study ([arXiv:2607.21656](https://arxiv.org/abs/2607.21656))
found cross-model review is **asymmetric and can be net negative**: one
direction lifted pass rates 71.6% → 89.7%, the reverse dropped them
91.4% → 82.8% — worse than no review at all.

Which is why the method carries a ledger.

## The ledger

Every finding gets reproduced by the executor before it is fixed, and recorded
as `CONFIRMED` / `REFUTED` / `UNVERIFIED` with the command and output that
settled it, plus a running tally. If REFUTED meets or beats CONFIRMED over ~5
phases, the reviews are buying misdiagnoses at 150-250k tokens each and the
method tells you to stop paying.

Supervision is itself a control, and an unmeasured control is the exact thing
this method exists to distrust.

## Prior art

Nothing here is novel in isolation, and it helps to know the names: this is
**generator–critic / actor–critic** review (the argument being correlated error
in self-review), applied to **spec-driven development** with phase gates (cf.
GitHub Spec Kit), where each gate is red-first (**TDD**) and then
**mutation-tested** (Lipton 1971; DeMillo, Lipton & Sayward 1978). The
combination — plus project-specific base rates and a precision ledger — is the
part that is hard to find off the shelf.

## License

MIT.
