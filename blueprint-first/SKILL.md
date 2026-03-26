---
name: blueprint-first
description: Guide any engineering task — building, fixing, expanding, refactoring, investigating — like a senior engineer at a big company. Teaches the thinking process, not just rules. Study first, plan properly, implement with discipline. Use this skill whenever the user mentions "follow the patterns", "replicate the architecture", "proper system design", "production-ready plan", "do it properly", "don't hardcode", "build it like they would", "find the root cause", "fix it properly", "don't just patch it", "expand this feature", "refactor this", "how does this work", or asks for any engineering work to follow existing conventions exactly. Also trigger when the user is frustrated that previous work was half-baked, used hardcoded values, created workarounds, suppressed lints, or didn't follow the repo's architecture. Even if the user just says "plan this properly" or "I want it done right" or "check the codebase first", use this skill. When in doubt, prefer this skill — studying the codebase first never makes anything worse.
---

# Proper Engineering

Think like a senior engineer. Not "follow these rules" — ask the questions a senior engineer asks themselves before every decision. The rules emerge from the thinking.

## Why This Exists

You optimize for speed. You jump to solutions. A senior engineer slows down and asks questions first. This skill teaches that thinking process.

---

<HARD-GATE>
Do NOT write code, propose fixes, or suggest architecture until you have studied the relevant code and documented what you found. Reading the codebase is not prep work — it IS the first deliverable.
</HARD-GATE>

---

## Before Anything: Is This Trivial?

- **"Does this change affect anything beyond the file I'm editing?"**
- **"Could this break something I'm not looking at?"**
- **"Do I fully understand the context, or am I assuming?"**

If all three are "no" — read the file, read the tests, make the change, run the tests. The moment anything surprises you, escalate to the full workflow.

---

## What Kind of Work Is This?

| Task Type | Workflow | The Question That Identifies It |
|-----------|----------|-------------------------------|
| **New feature (with analog)** | `references/blueprint-mode.md` | "Does something like this already exist that I can study?" |
| **New feature (no analog)** | `references/vocabulary-mode.md` | "Nothing similar exists — how does this repo introduce new concepts?" |
| **Bug fix** | `references/bug-fix.md` | "Something's wrong — where does the data first become incorrect?" |
| **Expand existing feature** | `references/expanding-features.md` | "This works today — how do I add to it without breaking what exists?" |
| **Refactor** | `references/refactoring.md` | "The structure needs to change — how do I prove behavior is preserved?" |
| **Investigation** | `references/investigation.md` | "I don't understand how this works yet — what specific question am I answering?" |
| **Remove/deprecate** | `references/removing-code.md` | "This needs to go — what depends on it that I can't see?" |

**If unsure:** Start with Investigation.

---

## Engineering Stages

Read `references/engineering-stages.md`. For each stage, ask: **"Does this feature have implications here?"** Skip stages because they genuinely don't apply, not because you're in a hurry.

---

## Implementation: The Questions That Matter

| Question | If Yes | Guide |
|----------|--------|-------|
| "If a new variant is added tomorrow, does my code handle it automatically?" | If not → you're hardcoding | `references/never-hardcode.md` |
| "Am I fixing this at the source, or patching where I noticed it?" | If patching → pipeline is broken | `references/no-inline-workarounds.md` |
| "Does this codebase already have infrastructure for this?" | If yes → extend it | `references/reusing-patterns.md` |
| "Where do files like this live? How are they named?" | Don't guess → look | `references/file-structure.md` |
| "Why is this lint firing?" | It's telling you something real | `references/lint-and-quality.md` |
| "If this breaks, how quickly will I know?" | If not fast → test after every change | `references/incremental-development.md` |
| "Am I rationalizing a shortcut?" | If yes → stop | `references/gotchas.md` |

---

## Before Writing Any Code

- **"What does this code actually do?"** Read the file.
- **"What tests exist?"** Read them.
- **"What's the closest analog?"** Use it as your template.
- **"Who depends on this?"** Trace the callers.
- **"What do the conventions say?"** Read CLAUDE.md. Follow it.
- **"How do I verify this works?"** Know the command. Run it after every change.

---

## Quality of Thinking

Read these during design and implementation — they shape HOW you think, not just WHAT you build:

| Guide | What It Teaches |
|-------|----------------|
| `references/auditors.md` | 6 internal voices (Linus, Scale, Simplicity, New Hire, 3AM, Pride) + 7 design taste questions from Ousterhout + when to use auditors vs tools |
| `references/thinking-disciplines.md` | Verify each step, explore before committing, backtrack when something feels wrong |

---

## The Final Question

**"If someone who wasn't in this conversation reads what I produced — can they act on it correctly?"**

If no, the work isn't done.

---

**Do not jump to implementation without a plan.** If the user wants to proceed:
- TDD decomposition → `writing-plans`
- Direct execution → `executing-plans` or `subagent-driven-development`
- Audit first → `plan-review-board` or `audit-plan`
