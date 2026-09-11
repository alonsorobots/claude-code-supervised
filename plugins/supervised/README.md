# supervised

A working method for agentic coding where **being confidently wrong is
expensive**: one model plans and reviews, another executes, gates are written
red before they are implemented and mutation-checked afterwards, and every
review finding is reproduced and recorded so you can tell whether the review is
earning its cost.

## Install — one line

```sh
claude plugin marketplace add alonsorobots/claude-code-supervised && claude plugin install supervised@supervised-tools
```

Then, in any session:

```
/supervised ~/.claude/plans/my-plan.md     # or: /supervised <a goal, to plan first>
```

That is the whole setup. **There is nothing to configure.**

## What it does

- `/supervised` — the method: phased execution, gate-first, mutation-checked,
  with an explicit "when this is NOT worth paying for" table so it stops you
  spending a review on a rename.
- `fable-reviewer` — an adversarial review agent that runs on a different model,
  reads diffs and RUNS commands, and is forbidden from reviewing off a summary.

## It configures itself

The thing that makes review worth paying for is **base rates**: the defects
*your* codebase actually produces. A reviewer with a generic checklist finds
generic things.

You do not write those. Every finding the executor confirms gets appended to
`.claude/REVIEW_BASE_RATES.md` in your repo as one line, as a byproduct of
reproducing it — which it was going to do anyway. The reviewer reads that file
before its own generic classes. So the first review is generic, the fifth knows
your repo, and nobody ever sat down to write a taxonomy.

If you *want* a head start, `REVIEW_BASE_RATES.template.md` in this plugin
explains how to seed it by hand. Entirely optional.

## The reviewer model

`agents/fable-reviewer.md` declares `model: fable`. If your account cannot reach
that model the command retries without the override and tells you the review ran
same-model — it degrades out loud rather than silently. Change the frontmatter
to any model you prefer.

## The honest caveat about "independence"

If the executor and the reviewer come from the same lab (e.g. Opus executing,
Fable reviewing — both Anthropic), you are NOT getting decorrelated blind spots.
Shared pretraining lineage and post-training mean the two models fail in similar
ways. What you still get, and it is worth real money:

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

Every finding is reproduced before it is fixed and recorded as `CONFIRMED` /
`REFUTED` / `UNVERIFIED` with the command and output that settled it, plus a
running tally. If REFUTED meets or beats CONFIRMED over ~5 phases, the reviews
are buying misdiagnoses at 150-250k tokens each and the method tells you to stop
paying.

Supervision is itself a control, and an unmeasured control is the exact thing
this method exists to distrust. As far as I know this is the only tool of its
kind that will tell you to stop using it.

## Prior art

Nothing here is novel in isolation, and it helps to know the names: this is
**generator–critic / actor–critic** review (the argument being correlated error
in self-review), applied to **spec-driven development** with phase gates (cf.
GitHub Spec Kit), where each gate is red-first (**TDD**) and then
**mutation-tested** (Lipton 1971; DeMillo, Lipton & Sayward 1978). The
combination — plus self-accruing base rates and a precision ledger — is the part
that is hard to find off the shelf.

## License

MIT.
