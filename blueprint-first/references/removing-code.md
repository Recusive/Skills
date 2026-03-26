# Removing and Deprecating Code

Removing code seems simple — just delete it. In practice, it's one of the most error-prone tasks because dependencies are invisible until they break.

## When to Remove

- Dead code that's never called (confirmed by grep, not by assumption)
- Deprecated features being replaced by a new implementation
- Workarounds that are no longer needed after a proper fix
- Temporary code (experiments, debugging aids, scaffolding)
- Backward-compatibility shims after sufficient migration time

## The Process

### Step 1: Confirm It's Actually Unused

"I don't see anyone calling this" is not proof. Confirm with evidence:

- **Grep the entire workspace** for the function/type/module name
- **Check re-exports** — is it exported from lib.rs even if not called internally?
- **Check config/CLI** — is it referenced in config files, command definitions, or docs?
- **Check tests** — test code that exercises it means it's tested intentionally, not dead
- **Check git blame** — was it recently added? Someone might be planning to use it
- **Check dynamic dispatch** — trait objects and function pointers don't show up in static grep

If ANY reference exists, it's not unused. Trace each reference to determine if the reference itself is dead (cascading dead code) or alive.

### Step 2: Understand Why It Exists

Before removing, understand why the code was written. Read the git log, PR description, or comments.

- **Was it intentional?** Some code looks dead but is a necessary default, fallback, or compatibility layer.
- **Is it conditional?** Code behind `#[cfg]` flags, feature flags, or platform-specific builds might be "dead" on your platform but alive elsewhere.
- **Is it recently added?** Someone might have committed the type but not the consumer yet. Check recent branches.

### Step 3: Remove Incrementally

Don't delete an entire module at once. Remove in layers:

1. Remove the consumers first (the code that calls the thing being removed)
2. Remove the implementation
3. Remove the type definitions
4. Remove the module declaration
5. Remove the test file
6. Clean up re-exports

After each step: run tests, run linter, run formatter.

### Step 4: Clean Up References

After removing code, clean up everything that referenced it:

- Import statements that now import nothing
- Re-exports in lib.rs or mod.rs
- Comments or docs that mention the removed code
- Config options that controlled the removed feature
- CLI flags or subcommands
- Error variants that only the removed code could produce

### Step 5: Verify Nothing Broke

- Run the full test suite for affected crates
- Run tests for crates that depended on the removed module
- Check that the build succeeds with no warnings
- If the removed code was part of a public API, check downstream consumers

## Deprecation (When You Can't Remove Yet)

Sometimes you can't remove code immediately — other teams depend on it, or users need migration time.

**Deprecation process:**
1. Mark as deprecated with a clear message pointing to the replacement
2. Add a timeline (when will it be removed?)
3. Ensure the replacement is fully functional before deprecating the old
4. Log a warning when deprecated code is used (if runtime)
5. Remove after the timeline passes

**Don't** leave deprecated code without a removal date. Undated deprecations never get removed.

## Removal Gotchas

**Removing something that's used dynamically.** Your grep missed it because it's called through a trait object, a function pointer, or string-based dispatch. Check ALL dispatch patterns, not just direct calls.

**Removing a "dead" field on a serialized type.** The field is no longer set in code, but it exists in serialized data on users' machines. Removing it from the struct breaks deserialization of old data. You need `#[serde(default)]` or a migration.

**Removing one side of a backward-compat pair.** The old API and new API coexist. You remove the old API, but some consumers still use it (in another crate, in user scripts, in config files). Verify ALL consumers are migrated first.

**Not cleaning up the breadcrumbs.** You remove the function but leave the import, the re-export, the comment that says "uses FooHelper for X," and the config option that controlled it. Now the codebase has ghost references to something that doesn't exist.
