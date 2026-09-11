# supervised-tools

Claude Code plugins.

## supervised

Two models instead of one. One writes the code, a different one checks it.

```sh
claude plugin marketplace add alonsorobots/claude-code-supervised && claude plugin install supervised@supervised-tools
```

Then `/supervised <a plan file, or just tell it what you want>`. Nothing to
configure.

Tests have to fail before they pass. It breaks your code on purpose to prove the
tests would notice. It keeps score on the reviewer and tells you to stop using
it if the reviewer turns out to be mostly wrong. And it quietly learns your
codebase's real bugs as you go.

[More →](plugins/supervised/)

MIT.
