# supervised-tools

A Claude Code plugin marketplace.

## Install

```
/plugin marketplace add alonsorobots/claude-code-supervised
/plugin install supervised@supervised-tools
```

## Plugins

### [supervised](plugins/supervised/)

Execute a phased plan with a **reviewer model that is not the author**. Gates
are written red before they are implemented and mutation-checked afterwards,
and every review finding is reproduced and recorded as CONFIRMED / REFUTED /
UNVERIFIED — so you can tell whether the review is earning its cost instead of
assuming it.

```
/supervised ~/.claude/plans/my-plan.md
```

See the [plugin README](plugins/supervised/) for what to customise before it is
worth much (short answer: your own base rates), and for the honest caveat about
how much "independence" two models from the same lab actually give you.

## License

MIT
