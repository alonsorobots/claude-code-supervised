# supervised-tools

A Claude Code plugin marketplace.

## Install — one line, nothing to configure

```sh
claude plugin marketplace add alonsorobots/claude-code-supervised && claude plugin install supervised@supervised-tools
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

Works out of the box. It learns your codebase's real defect patterns as you use
it, by appending every confirmed finding to `.claude/REVIEW_BASE_RATES.md` — so
the fifth review is sharper than the first without anyone configuring anything.

See the [plugin README](plugins/supervised/) for the honest caveat about how
much "independence" two models from the same lab actually give you, and for the
ledger that will tell you to stop using this if it stops paying off.

## License

MIT
