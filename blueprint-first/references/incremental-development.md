# Incremental Development and Backward Compatibility

## Make Small, Verifiable Changes

In a large codebase, a mistake in one crate cascades through downstream crates. The only way to stay in control is to make small changes and verify each one.

### The Rhythm

1. Add the type → test it compiles → commit
2. Add the implementation → test it works → commit
3. Wire into consumer → test integration → commit

Never batch 5 changes into one giant diff. When something breaks, you need to know which change caused it.

### Why This Matters

Consider adding a new field to `ModelInfo` in the `protocol` crate:
- `protocol` compiles fine
- But `core` constructs `ModelInfo` in 8 places — all need the new field
- `tui` pattern-matches on `ModelInfo` in 3 places — might need updating
- Tests in 4 crates create `ModelInfo` directly — all need the new field

If you also changed the cache format, the API client, and two consumer functions in the same batch, you're debugging 20 compilation errors across 5 crates with no idea which change caused which error.

## Cross-Crate Impact Assessment

Before changing any type or function that's used outside its crate:

1. **Grep for the type/function name** across the workspace
2. **List every file that constructs, matches, or calls it**
3. **Plan your update order** — foundation crates first, consumers last
4. **Test each crate as you update it** — `cargo test -p <crate>`

## Backward Compatibility

### Serialized Types
New fields on types that get serialized (config, protocol, cache) need backward compat:
```rust
#[serde(default)]                    // Existing data without this field still deserializes
pub new_field: Option<NewType>,      // None for legacy data
```

### Enum Variants
New variants can break `match` statements:
- Check who matches on the enum
- If they use wildcard (`_`) arms → safe to add
- If they list all variants → they need updating

### Config Options
New config options must have sensible defaults:
- The default should preserve current behavior
- Users who don't touch their config should see no change
- Document the new option in config examples

### Tests
Existing tests should pass WITHOUT modification:
- If a test breaks, your change violated a contract
- Fix your change, not the test
- The exception: if the plan explicitly calls for behavior change

## The Commit Strategy

Each commit should be:
- **Buildable** — the entire workspace compiles
- **Testable** — tests for the changed crate pass
- **Reviewable** — small enough to understand in isolation
- **Revertable** — if it causes problems, reverting it doesn't break other work

Don't commit broken intermediate states "to save progress." If you need a checkpoint, use git stash or a WIP branch.
