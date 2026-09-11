---
description: Execute a plan with a reviewer model that is not the author (/supervised <plan path or goal>)
argument-hint: [plan path, plan name, or a goal to plan first]
---

Execute work under supervision: **the reviewer model plans and reviews, the
executor model implements.** As shipped that is Fable reviewing Opus; the
reviewer model is set in the `fable-reviewer` agent's frontmatter and you may
change it.

**What independence this actually buys — do not overclaim it.** If both models
come from the same lab, this is NOT vendor-diverse review: shared pretraining
lineage and shared post-training mean the blind spots are *correlated*, not
independent. Claim less here than a genuinely cross-vendor pairing may claim.
What survives is real but narrower:

1. **Fresh context** — the reviewer never saw the reasoning that produced the
   code, so it cannot inherit a wrong premise by having agreed to it earlier.
   This is the largest share of the value and it is model-independent.
2. **No authoring commitment** — it is not defending work it wrote.
3. **A different model** — different scale and generation decorrelate *some*
   errors. Treat this as a bonus, not the thesis.

The load-bearing part is neither: it is the **base-rate section of the
reviewer's contract**, which names defects THIS codebase actually produced. No
model of any vendor looks for those unprompted. If you have not filled in
`.claude/REVIEW_BASE_RATES.md` for this repo, the reviews will be generic —
see the plugin README.

<plan-arg>$ARGUMENTS</plan-arg>

# 0. Scope and lifetime — READ THIS BEFORE RE-INVOKING

**You do not need to type `/supervised` again for each new task.** Once these
instructions are in context they govern the session: keep planning with the
reviewer model, keep reviewing at phase boundaries, without being reminded.

**It ends when the context does.** There is no timer. It lapses when the
session ends, or when the conversation grows long enough that this block is
summarised away — and it lapses SILENTLY, which is the same
control-that-cannot-fail failure this method exists to catch. Two defences:

- **Executor:** the first time you act under this command, append a
  `## Method` line to the plan file naming this command as the governing
  contract. After a compaction, that line is how you discover supervision was
  in force. If you are working from a plan that carries such a line and these
  instructions are NOT in your context, say so and re-read this file before
  continuing.
- **User:** re-invoking is harmless but redundant. Re-invoke only after a
  `--continue`/`--resume`, or if the executor has visibly stopped spawning
  reviewers.

**When to STOP using it.** Supervision is not free — a single review runs
150-250k tokens, and a planning call can exceed that. Pay for it when the
dominant risk is *being confidently wrong in a way that looks right*:

  WORTH IT                                  NOT WORTH IT
  deployed or irreversible work             small, obviously-checkable edits
  novel algorithms, no reference impl       work a test suite already gates
  a wrong answer that still looks plausible a wrong answer that fails loudly
  you would otherwise review your own work  throwaway or exploratory spikes
  numbers that will be quoted to someone    formatting, renames, scaffolding

The tell is the cost asymmetry: if being wrong is cheap to discover and cheap
to fix, run unsupervised and move faster. If being wrong produces a
plausible artefact that someone will act on, pay for the second model.

When you judge a task falls on the right-hand column, SAY SO and proceed
unsupervised rather than silently spending a review on it.

# 1. Resolve the argument

- A path or a plan name → look in that path, then `~/.claude/plans/`.
  **Read the whole plan before doing anything.**
- A bare goal with no plan → **one reviewer-model call to write the plan
  first** (§2).
- Empty → ask which plan. Do not guess.

# 2. If a plan must be written

Spawn the reviewer model with the goal, the repo, and instructions to produce a
phased plan: each phase independently shippable, with an explicit **GATE**
stating what would prove it works AND what would prove it does not. Save to
`~/.claude/plans/<slug>.md`. Include a §progress section for the executor to
append reds to, headed by the review ledger and its tally line (§5).

# 3. Execute — the default state

Work phase by phase. For each phase:

- Write the gate FIRST and **run it red** before implementing. Paste the verbatim
  red into the plan's progress section.
- Implement. Re-run to green.
- **Mutation-check every gate**: break the thing it guards, confirm it goes red,
  restore. A control you never made fail is a control you did not check.
- Full suite against a **known baseline** (a stashed clean tree or an isolated
  worktree — never a remembered number; a shared checkout may hold other
  agents' work).
- Commit **and push**. Stage explicit paths, never `git add -A`.
- Record deviations from the plan and why. A reasoned, recorded deviation is how
  the plan improves; an unrecorded one is the problem.

Do not stop at a phase boundary to ask permission to continue. Keep going.

# 4. Review — SPARINGLY. This is the expensive call.

**One review per phase boundary, and only when you are already confident.**

Bar to clear BEFORE spawning:
- the phase's spec is done, audited against the spec rather than memory;
- every gate has a red you RAN, in the plan;
- full suite green against a clean baseline;
- committed AND pushed.

Do **not** spawn a review to decide whether something is ready, to check a
single uncertain change, or to outsource verification you could run yourself.

## HOW to invoke it — the exact call

Use the **`Agent` tool**. The agent file carries both the contract and the
model.

```
Agent(
  subagent_type: "fable-reviewer",    # the contract + model come from the agent file
  run_in_background: true,            # you keep working; result arrives as a notification
  description: "Reviewer checks Phase D",
  prompt: "<the brief>"
)
```

**Fallback, if the host rejects that `subagent_type`.** Some builds do not offer
custom agent types, and a newly installed agent may not register until the
session reloads. Then select the model explicitly and pass the contract by
reference:

```
Agent(subagent_type: "general-purpose", model: "fable", run_in_background: true,
      prompt: "Read ${CLAUDE_PLUGIN_ROOT}/agents/fable-reviewer.md in full first — it is your contract. …")
```

`model:` overrides the model in agent frontmatter, so it is also how you force a
particular reviewer model for a one-off review without an agent file at all.

**The prompt must carry**, or the review is worth little:
1. `Read your contract in full first` (the agent file, if invoked by reference).
2. The plan path, and which phase/section is under review.
3. The exact commits (`git log --oneline`), and that they are pushed.
4. The **exact test command including the interpreter** — the wrong one collects
   nothing and still exits 0.
5. Your specific claims, framed as *things to check, not believe*.
6. **Where you are least sure.** This is where a reviewer earns its cost; say it
   outright rather than hoping it gets found.
7. `Do not edit files — you are reviewing.`

**Continuing a review instead of starting one.** A follow-up to a reviewer that
already has the context is much cheaper than a fresh review: use
`SendMessage(to: "<agent id or name>", message: …)`. Start a NEW Agent call only
for a new phase.

Results arrive as a task notification. Do not predict or fabricate them; if
asked before it lands, say it is still running.

# 5. Act on findings

**Reproduce each finding yourself before fixing it.** A reviewer can be wrong,
and a fix aimed at a misdiagnosis is worse than no fix. Then fix, gate,
mutation-check, full suite, commit, push, and record the finding + its verbatim
red in the plan. Return to §3.

## The ledger — the only way this method learns

You already reproduce every finding, so the verdict costs nothing extra. WRITE
IT DOWN. One line per finding in the plan's progress section:

    [phase] CONFIRMED  <finding> — reproduced by: <command + the red it printed>
    [phase] REFUTED    <finding> — disproved by: <command + the green it printed>
    [phase] UNVERIFIED <finding> — could not reproduce either way, and why

Keep a running tally at the top of that section:
`reviews: N | confirmed: C | refuted: R | unverified: U`.

**Why this is mandatory.** Supervision is itself an unmeasured control — the
exact thing this whole method exists to distrust. Cross-model review is not
automatically positive: in a 116-task controlled study (arXiv:2607.21656) one
direction of pairing lifted the pass rate 71.6% → 89.7% while the reverse
direction DROPPED it 91.4% → 82.8%, i.e. worse than no review at all. Whether
YOUR pairing is a good direction is an empirical question about *those* two
models on *this* codebase, and the tally is the only measurement of it.

**Act on the tally.** If REFUTED meets or beats CONFIRMED across ~5 phases, the
reviews are buying misdiagnoses at 150-250k tokens each: say so plainly, stop
spawning them, and propose either a genuinely vendor-diverse reviewer or
dropping supervision for this class of work. A confirmed rate that stays high
is the evidence that justifies continuing to pay.

# 6. Loop until

- the plan is done — then say what remains open and why; or
- a question is genuinely blocking: proceeding under any assumption would be
  unsafe or would waste the work if wrong. Ordinary judgment calls are yours to
  make. Say what you would do by default, so a one-word answer unblocks you.

# Pairing with `/goal`

They do different jobs and compose — but as **two messages, not one, ever**.

```
/supervised ~/.claude/plans/my-plan.md
/goal phases D-G done, suite green, everything pushed
```

`/supervised` goes FIRST: setting a goal starts a turn immediately, so doing
that first spends a turn before the method is loaded.

**Why they cannot share a message.** Slash commands are recognised only at the
START of a message — mid-message is literal text. Stacking (`/a /b args`) exists
from **v2.1.199** for user-invocable skills, but `/goal` is a BUILT-IN and
built-ins do not stack. And stacked skills all receive the SAME `$ARGUMENTS`, so
a condition and a plan path could not be routed separately anyway.

`/goal` supplies what this file cannot: a real evaluator that re-invokes after
every turn until the condition is met or judged impossible. This command
supplies the method.

**Writing the condition** (up to 4,000 chars — multi-sentence is fine):
the evaluator reads only the TRANSCRIPT. It runs no commands and opens no
files, so every clause must be something the executor SURFACES. "Suite green"
counts only if the run is in the conversation. Give it one measurable end
state, the check that proves it, and any constraint that must hold on the way.
Bound it with "or stop after N turns" if it could run away.

**Expect quiet stretches.** A turn that ends with a subagent or background shell
still running SKIPS evaluation — and this workflow backgrounds reviews and test
suites constantly. It resumes on the next turn that ends clean; background
results arrive as fresh turns. Idle check-ins start at 30 min and back off.

One goal per session; `/goal clear` ends it. It survives `--continue`/`--resume`.

# Standing rules

- **Verify, do not believe** — yours or the reviewer's. Claims are not evidence.
- **The dominant defect is a check that cannot fail**, or one that passes
  *because* of the flaw. Suspect it before suspecting the code.
- Report the numbers plainly: N passed, which failures are pre-existing, what
  you did not verify.
