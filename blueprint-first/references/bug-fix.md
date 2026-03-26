# Bug Fix Workflow

Fixing bugs is NOT "find something that looks wrong, change it, hope it works." A proper engineer treats bug fixing as a diagnostic process — like a doctor who runs tests before prescribing treatment.

## The Process

### Step 1: Reproduce First

Before touching any code, reproduce the bug. You need to see it happen.

- What is the exact user scenario that triggers it?
- What is the expected behavior?
- What is the actual behavior?
- Can you reproduce it consistently, or is it intermittent?

If you can't reproduce it, you don't understand it. Keep investigating until you can trigger it on demand. An intermittent bug that you "think" you've fixed is a bug that will come back.

For intermittent bugs: measure the reproducibility rate. Is it 1 in 20 or 1 in 2? That rate tells you whether your fix worked — if the rate doesn't change, you fixed the wrong thing.

### Step 2: Establish What Normal Looks Like

Before looking at the broken case, look at the WORKING case. Read the logs, trace the data flow, understand the output when the system behaves correctly. Without a baseline of "normal," you can't recognize what's actually abnormal. The bug might not be where you think — it might be that the "broken" behavior is actually correct and your expectation is wrong.

### Step 3: Read Before Fixing

Don't guess at the fix. Read the code that's involved.

- Read the function where the bug manifests
- Read the functions that call it
- Read the functions it calls
- Understand the INTENDED behavior, not just the current behavior

The most common mistake: you see a line that looks wrong, you change it, and you break something else because you didn't understand why it was written that way.

### Step 4: Trace to Root Cause

The bug is visible in one place but the root cause is usually somewhere else. Trace backward from the symptom.

**Use binary search to narrow the problem space.** Don't trace every line — halve the possibilities at each step. Is the data correct at the midpoint of the pipeline? If yes, the bug is downstream. If no, it's upstream. Each question should eliminate roughly half the remaining code. This turns a 6-layer trace into 3 checks.

**The display shows the wrong value.** Is the display code wrong, or is the data wrong? If the data is wrong, trace where it came from. Was it wrong in the API response? In the mapping layer? In the config override? In the cache?

Each step backward gets you closer to the root cause. The fix belongs where the data first becomes wrong — not where you first notice it's wrong.

**Ask yourself:** "If I fix it here, are there other places that consume the same wrong data?" If yes, you're not at the root cause yet.

### Step 4: Check for the Same Bug Elsewhere

Bugs exist in patterns. If the OpenAI path has this bug, check the Anthropic path. If one endpoint mishandles null values, check the other endpoints.

Grep for the pattern that caused the bug. How many other places use the same broken pattern? Fix them all, not just the one that was reported.

### Step 5: Write the Test First

Before writing the fix, write a test that reproduces the bug.

```
1. Write a test that captures the broken behavior
2. Run it — it should FAIL (proving the bug exists)
3. Write the fix
4. Run it — it should PASS (proving the fix works)
5. Run ALL related tests — they should still PASS (proving nothing else broke)
```

This gives you:
- Proof the bug existed (the failing test)
- Proof the fix works (the passing test)
- A regression test that prevents the bug from returning

### Step 6: Fix at the Right Layer

Read `no-inline-workarounds.md`. The fix belongs at the root cause, not at the symptom.

**Bad:** The status bar shows the wrong context window, so you add special-case logic in the status bar code.

**Good:** The model metadata pipeline produces the wrong context window value, so you fix the pipeline. The status bar now shows the correct value without changes.

### Step 7: Check for Collateral Damage

Your fix might break something else. Before declaring done:

- Run the full test suite for the affected crate(s)
- If you changed a shared type or function, run tests for downstream crates too
- If the fix changes behavior (not just corrects it), check if anything depends on the old (broken) behavior

### Step 8: Verify End-to-End

Not just "the test passes" but "the actual user scenario works."

If the bug was "context window shows wrong value in the TUI," launch the actual application, trigger the exact scenario, and verify the display is correct. Unit tests can pass while the real system is still broken — they test in isolation, not in context.

## Common Bug Fix Mistakes

**Fixing the symptom.** The status bar shows "0" instead of "200000." You add `if value == 0 { value = 200000 }` in the display. The actual problem is that the value was never populated in the pipeline. Your "fix" will show 200000 for every model that legitimately has 0 or missing context window.

**Not reproducing first.** You read the code, think you see the bug, and fix it. But you were looking at the wrong code path. The actual bug is elsewhere, and now you've changed code that was correct.

**Fixing one instance.** The bug exists in 3 places. You find and fix 1. The other 2 remain, waiting to be discovered later.

**Not writing a test.** You fix the bug, verify it manually, ship it. Six months later, a refactor reintroduces the same bug. No test catches it because you never wrote one.

**Declaring "fixed" without end-to-end verification.** The unit test passes. But the unit test mocks the data pipeline and only tests the display logic. The actual data pipeline is still broken. The bug is "fixed" in tests and broken in production.
