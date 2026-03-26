# Lint Enforcement and Code Quality

## The Rule

Never suppress a lint. Fix the underlying issue. No exceptions.

## Authoritative Source

The repo's CLAUDE.md (or equivalent conventions file) has the complete list of forbidden suppression patterns for every language. **Read CLAUDE.md first** — it's the authoritative source. What follows here is the universal principle that applies to ANY repo, even those without a conventions file.

## The Universal Principle

Every lint exists because someone encountered a real bug caused by the pattern it detects. Suppressing the lint doesn't fix the bug — it hides it.

- **Dead code warning?** Delete the dead code. If you're planning to use it later, it doesn't exist yet.
- **Unused import?** Remove it. Add it back when you need it.
- **Type mismatch?** Fix the type or fix the code. Don't cast or suppress.
- **Linter complains about a pattern?** Refactor to satisfy it. The denied lints exist for a reason.
- **Crate/file-level suppression?** Never. It masks problems across too wide a scope.

## The One Exception

Platform-specific conditional compilation where an import is used on one platform but not another. Even then, prefer gating the import itself rather than suppressing the warning.

## When a Lint Seems Wrong

1. Investigate WHY it's firing
2. Nine times out of ten, the lint is right and your code has a real issue
3. The tenth time, the correct response is to file a ticket against the linter, not suppress it

## Testing After Every Change

Run the linter and tests after EVERY individual change, not at the end. The repo's CLAUDE.md will tell you the exact commands. The universal pattern:

```
1. Make one change
2. Run linter (clippy, eslint, ruff, etc.)
3. Run tests for the affected package
4. Run formatter
5. Repeat
```

Catching a problem immediately is infinitely cheaper than catching it after 10 changes stacked on top.

## Formatting

Run the repo's formatter after every change. Don't ask for permission. Just run it. Every repo with a formatter config has specific rules your manual formatting won't match. Let the formatter do its job.
