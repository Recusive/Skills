# How to Think Better

These three disciplines separate rigorous engineering from "seems about right." They map to how the best reasoning systems are trained: verify each step, explore before committing, and correct yourself when something's off.

## Verify Each Step, Not Just the Final Answer

Don't wait until the plan is complete to check if it's right. After each phase of work, stop and verify that step is solid before building on it.

- After tracing the architecture → "Does this trace match reality? Let me verify one layer by reading the actual code path end-to-end."
- After designing a layer → "Does this design actually work with the integration points I documented? Let me check the interface it needs to connect to."
- After writing a function → "Does this do what I think? Let me run the test immediately, not after 5 more functions."

The point: a wrong step 2 makes steps 3-7 worthless. Catching it immediately costs seconds. Catching it at the end costs hours. Use the real tools — compiler, tests, linter — as your objective truth at every step. Don't reason about whether code works; RUN it and find out.

## Reflect After Each Step, Not Just Verify

Verification is mechanical: did the test pass? Reflection is cognitive: what did I learn?

After each step, ask: "Did that step change my understanding of the problem? Should I adjust my plan?" The architecture trace might reveal that the system works differently than you assumed. The test result might show an edge case you hadn't considered. The lint error might point to a design flaw, not a syntax issue.

Verification catches errors. Reflection catches wrong assumptions. Both matter — but most engineers do the first and skip the second.

## Explore Before Committing

Before locking in an approach, consider at least one alternative. Not "brainstorm 10 options" — just genuinely ask: "Is there another way to do this that might be simpler, more aligned with existing patterns, or more robust?"

- If you're adding a new type, ask: "Could I extend an existing type instead?"
- If you're creating a new module, ask: "Could this live inside an existing module?"
- If you're designing a 6-layer pipeline, ask: "Is there a 4-layer version that's equally correct?"

The first approach that comes to mind is optimized for speed, not correctness. The second approach you consider is where taste lives. You don't have to pick the second one — but you have to consider it. If you can't articulate why your chosen approach is better than the alternative, you haven't thought about it enough.

## Backtrack When Something Feels Wrong

If something doesn't fit — a test fails unexpectedly, a type doesn't line up, the design feels forced — that's a signal. Don't push through. Stop and ask: "Am I on the wrong path?"

- **Unexpected test failure** → Don't "fix" the test. Ask why your assumption was wrong.
- **Type mismatch** → Don't cast. Ask why the types disagree — one of your layers has the wrong model.
- **Design feels complex** → Don't add more abstraction. Ask if the approach itself is wrong.
- **You're writing a workaround** → Stop. Backtrack to where the data first becomes wrong.

The cost of backtracking early is one discarded approach. The cost of pushing through is a finished implementation built on a flawed foundation.
