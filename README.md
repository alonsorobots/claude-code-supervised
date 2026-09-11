# supervised-tools

Claude Code plugins.

## supervised

**Coding agents grade their own homework.** The same model writes the code and
the test, so if it misunderstood something, the test has the same
misunderstanding in it. It passes because of the bug. That's how you get green
checkmarks on code that breaks next week.

This makes a second model check the work — one that never saw how the code got
written, so it can't agree with a mistake it already made.

### Install

Paste this into your terminal:

```sh
claude plugin marketplace add alonsorobots/claude-code-supervised && claude plugin install supervised@supervised-tools
```

### Use

In Claude Code:

```
/supervised add rate limiting to the API
```

Nothing to configure. Tests have to fail before they pass. It breaks your code
on purpose to prove the tests would notice. It keeps score on the reviewer and
tells you to stop using it if the reviewer turns out to be mostly wrong. And it
learns your codebase's real bugs as you go.

[Full details →](plugins/supervised/)

MIT.
