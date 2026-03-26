# Expanding an Existing Feature

Expansion is different from building new. The feature already works, has users, has tests, and has assumptions baked in. Your job is to add capability without breaking any of that.

The danger: you treat the expansion as a new feature and break the existing one. Or you treat it as a small change and miss the ripple effects.

## The Process

### Step 1: Understand the Current Feature Completely

Before expanding, you must know what exists. Not "I scanned the file" — you must understand:

- **What does it do today?** Walk through the user scenario end to end.
- **What are the edge cases?** What happens with empty input, missing config, network failure?
- **What assumptions does it make?** Does it assume only one provider? Only one format? Only certain types?
- **What tests exist?** Read every test. They document the expected behavior.
- **What config controls it?** What can users change?

If the feature is large, trace it the same way you would in blueprint mode — layer by layer, entry point to output.

### Step 2: Identify the Seams

Every feature has places where it was designed to be extended and places where it wasn't.

**Designed-in seams:**
- Enum variants (adding a new variant is natural)
- Trait implementations (adding a new impl is natural)
- Config options (adding a new option is natural)
- Plugin/hook points (adding a new handler is natural)
- Collection types (adding a new item to a Vec/Map is natural)

**Not designed for extension:**
- Hardcoded `match` with no wildcard
- Function that assumes a specific number of cases
- Struct with fields that implicitly cover all variants
- Logic that uses `if/else` chains for specific values

If your expansion needs to go through a "not designed for extension" point, that's a signal you might need to refactor the extension point first (see `refactoring.md`), then expand.

### Step 3: Preserve Existing Behavior

This is non-negotiable. The existing feature works. Users depend on it.

**Rules:**
- Existing tests must pass WITHOUT modification
- Existing config must produce the same behavior with no changes
- Existing data formats must continue to work (new fields need defaults)
- Existing API contracts must not change

If an existing test breaks, your expansion violated a contract. Fix your expansion, not the test. The one exception: if the expansion intentionally changes behavior (documented and approved), update tests with clear commit messages explaining why.

### Step 4: Follow the Feature's Own Patterns

The feature has internal patterns — how it handles errors, how it names things, how it structures its functions. Your expansion should look like the original author added it.

- If the feature uses a builder pattern, your expansion uses the builder
- If it handles errors with a specific enum, your expansion adds a variant to that enum
- If it tests with specific helpers, your expansion uses the same helpers
- If it logs at a specific level, your expansion logs at the same level

Don't introduce a new pattern into an existing feature. That's how features become internally inconsistent.

### Step 5: Design the Expansion

Now design. The design should answer:

1. **What new behavior is being added?** Specific, testable description.
2. **Which existing layers are affected?** List every file that needs changes.
3. **Which seams are being used?** Show that the expansion goes through natural extension points.
4. **What new tests are needed?** Both for the new behavior AND for "existing behavior still works."
5. **What config changes are needed?** New options must have defaults that preserve existing behavior.
6. **What's the backward compat story?** Can old data/config work with new code? Can new data/config work with old code?

### Step 6: Implement Incrementally

Read `incremental-development.md`. For expansions, the order matters:

1. Add new types/fields with defaults → existing tests still pass
2. Add new logic behind the new types → existing behavior unchanged
3. Wire new logic into existing paths → both old and new behavior work
4. Add new tests → prove new behavior
5. Update docs/config examples → make it discoverable

Each step should pass all tests. If step 3 breaks existing tests, you need to rethink step 3.

## Expansion Gotchas

**Breaking the zero-change test.** If you haven't changed any config or input but existing tests fail, your expansion has a side effect you didn't intend. This is the most important signal during expansion.

**Adding a config option with no default.** Existing users don't have this option in their config. If your code panics when it's missing, you just broke every existing installation.

**Duplicating instead of extending.** Instead of adding a variant to the existing enum, you create a second enum. Instead of extending the existing handler, you write a parallel handler. Now there are two code paths doing similar things.

**Not reading the tests.** The existing tests document the feature's contracts. If you don't read them, you don't know what contracts you need to preserve.
