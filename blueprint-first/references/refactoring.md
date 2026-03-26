# Refactoring Workflow

Refactoring changes the structure of code without changing its behavior. The emphasis is on "without changing behavior" — the moment you change what the code does, you've left the refactoring domain and entered bug-fix or feature-expansion territory.

## When to Refactor

Not "it looks messy." Refactoring has a cost. You need a reason that justifies it.

**Good reasons:**
- Module is 800+ LoC and you need to add new functionality to it (CLAUDE.md convention)
- Error handling is inconsistent and causing real bugs
- A function does 3 things and you need to change one of them
- Code is duplicated in multiple places and you're about to add a third copy
- The abstraction no longer fits — every new feature requires a workaround

**Not good enough:**
- "It could be cleaner" (if it works and nobody needs to change it, leave it)
- "I'd write it differently" (the existing style is the repo's style)
- "While I'm here" (scope creep — do the task you were asked to do)

## The Process

### Step 1: Define "Done"

Before changing anything, write down:
- What does the code look like after the refactor?
- What's the measurable improvement? (Smaller files, clearer boundaries, eliminated duplication, better testability)
- What does NOT change? (External behavior, API contracts, test outcomes)

If you can't articulate what "done" looks like, you're not ready to refactor.

### Step 2: Ensure Test Coverage

You need tests that capture current behavior BEFORE you change anything. These tests are your safety net — they prove you haven't changed behavior.

- Run existing tests. Do they pass? Do they cover the code you're refactoring?
- If coverage is thin, write additional tests for the current behavior FIRST, in a separate commit.
- The refactoring commit should NOT include new test logic — only moved tests following their code.

### Step 3: Small, Behavior-Preserving Steps

Each step should:
1. Make ONE structural change
2. Pass ALL existing tests
3. Be independently reviewable
4. Be independently revertable

**Example — extracting a module from a large file:**
```
Step 1: Create the new file with the extracted functions → tests pass
Step 2: Update imports in the original file → tests pass
Step 3: Move related tests to sibling test file → tests pass
Step 4: Update re-exports in mod.rs/lib.rs → tests pass
```

Never "let me just move everything at once." If you move 5 functions and tests break, which move caused it?

### Step 4: No Behavior Changes During Refactor

If you find a bug during refactoring, DO NOT fix it in the same commit.

1. Note the bug
2. Finish the refactoring step you're on
3. Commit the structural change
4. Fix the bug in a SEPARATE commit with its own test

This keeps refactoring commits "behavior-preserving" and bug fixes clearly labeled. Mixing them makes it impossible to know if a test failure is from the refactor or the bug fix.

### Step 5: Verify Continuously

After EVERY step:
- Run the full test suite for affected crates
- Run the linter (`just fix -p <crate>` or equivalent)
- Run the formatter

Don't batch these checks. A test failure after step 1 is easy to diagnose. A test failure after steps 1-4 could be any of them.

## Types of Refactoring

### Extract Module
Moving code from a large file into its own module. Follow `file-structure.md` for where the new file goes, how it's declared, how types are re-exported.

### Extract Function
Pulling a block of code into a named function. The function should have:
- A clear name that describes WHAT, not HOW
- Parameters that match the data it needs (no grabbing from global state)
- Return type that matches what the caller expects

### Rename
Renaming a type, function, or module to better reflect what it does. Use the repo's naming conventions. Search for ALL usages — including docs, comments, and config files.

### Simplify
Reducing complexity: eliminating dead branches, collapsing nested ifs, removing indirection that serves no purpose. Every simplification must preserve behavior — verify with tests.

### Consolidate Duplication
Two or more places doing the same thing → one shared implementation. Be careful: make sure the duplication is ACTUAL (same intent, same behavior) and not COINCIDENTAL (same code today but different reasons that might diverge).

## Refactoring Gotchas

**"While I'm here" scope creep.** You start extracting a module and end up also changing the error handling, renaming three types, and adding a new feature. Each of those is a separate task. Do the extraction, commit it, then decide what's next.

**Changing behavior accidentally.** You refactor a function and subtly change its edge-case behavior. No test catches it because there's no test for that edge case. Months later, a user reports a regression. This is why Step 2 (ensure test coverage) matters.

**Breaking public API.** You rename an internal function that turned out to be used by another crate. Always grep for usages across the entire workspace before renaming.

**Refactoring untested code.** If there are no tests for the code you're refactoring, you have no way to verify behavior is preserved. Write tests first, then refactor.
