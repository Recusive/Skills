---
name: problem-clarifier
description: Clarify problems, bugs, and feature requests through a reflect-back interview before planning. Use this skill whenever the user describes an issue, bug, broken behavior, or a feature they want — BEFORE entering plan mode. Triggers on phrases like "this isn't working", "I'm seeing a bug", "something is wrong with", "I want to change how X works", "X is broken", "the problem is", "I'm having an issue with", "can you fix", or any description of unexpected behavior.
---

# Problem Clarifier

You are about to help someone articulate a problem they're experiencing. Your job is NOT to solve it, NOT to investigate code, and NOT to start planning. Your only job right now is to **understand exactly what the problem is** by reflecting it back until you and the user are on the same page.

## Why this matters

When someone describes a problem in natural language, the words they use rarely capture the full picture. They might say "it's not turning blue" when the real issue is "it turns blue but then reverts on the next keystroke." If you plan based on the first description, you'll build a plan for the wrong problem. That wastes everyone's time and erodes trust.

The fix is simple: before planning anything, play back what you heard and let the user correct you. Do this until they say "yes, that's exactly it."

## The Reflect-Back Loop

This is the core mechanic. It works like this:

### 1. Listen to the initial description

The user will describe their problem. Read it carefully. Pay attention to:
- What they're trying to do (the intent)
- What they expected to happen
- What actually happened instead
- Any specific triggers or steps they mention ("when I press space", "after I click submit")

### 2. Explore the relevant code

Before reflecting back, quickly explore the codebase to understand the area the user is talking about. This makes your reflect-back much more precise — you can use actual component names, function names, and file paths instead of vague descriptions. The user doesn't need to know the exact file path — that's your job.

Use Glob, Grep, and Read to understand the relevant code area. Keep this fast — you're building context, not debugging.

### 3. Reflect back with precision

Now restate the problem in your own words, using specific technical details from the code. Use the **AskUserQuestion** tool with a detailed restatement and ask if you've got it right.

The restatement should be:
- **Specific**: Include the actual UI element, component, file, or flow involved
- **Step-by-step**: Describe the exact sequence of user actions and what happens at each step
- **Clear about the gap**: State what SHOULD happen vs what DOES happen

**Example of a BAD reflect-back:**
> "So the slash commands aren't styled correctly?"

**Example of a GOOD reflect-back:**
> "Let me make sure I understand: You type a slash command like `/commit` in the chat input. It correctly appears styled (blue badge/highlight) while you're typing. But the moment you press Space after the command, the styling disappears and it becomes plain white text — even though the slash command text itself is still there. The problem is the styling is lost on Space, not that it never appears at all. Is that right?"

The good version is specific enough that if ANY part of it is wrong, the user can immediately point to exactly what's off.

### 4. Listen to corrections

The user will do one of three things:
- **Confirm**: "Yes, that's exactly it" -> proceed to the problem statement
- **Partially correct**: "No, the styling never appears at all" -> update your understanding, reflect back again
- **Redirect entirely**: "No, that's not what I mean at all" -> start fresh, ask what they're actually experiencing

For partial corrections, fold the new information into your mental model and do another reflect-back. Don't just ask "what did I get wrong?" — instead, restate the FULL problem with corrections applied. This way the user can verify the complete picture, not just the delta.

### 5. Repeat until confirmed

There is no limit on iterations. Whether it takes 2 rounds or 10 rounds, keep going until the user explicitly confirms. Look for clear confirmation signals:
- "Yes, that's it"
- "Exactly"
- "Yes, go for it"
- "That's the problem"
- "Correct, now plan it"

Do NOT assume confirmation from ambiguous responses like "yeah sure" or "I guess." If you're not sure, ask: "I want to make sure we're 100% aligned — is [restatement] exactly the issue, or is there a nuance I'm missing?"

## After Confirmation: The Problem Statement

Once the user confirms, produce a structured output that will feed into planning:

```
## Problem Statement

**What the user is trying to do:**
[The user's goal/intent in 1-2 sentences]

**Current behavior (the bug/issue):**
[Step-by-step description of what currently happens, including the specific trigger]

**Expected behavior:**
[What should happen instead]

**Key constraint or context:**
[Any important context — e.g., "this only happens after a rewind", "this is a new feature, not a fix"]

## Investigation Areas

These files and areas are likely involved:

1. **[file/component name]** — [why it's relevant, what to look at]
2. **[file/component name]** — [why it's relevant]
3. **[file/component name]** — [why it's relevant]

## Suggested Next Step

Enter plan mode to design a fix/implementation based on the problem statement above.
```

After outputting the problem statement, tell the user: **"When you're ready, put me in plan mode and I'll build a plan based on this problem statement."**

Do NOT enter plan mode yourself. Do NOT start planning. The user controls when planning begins.

## Important Behaviors

**Stay in interview mode.** Your instinct will be to start debugging or proposing solutions. Resist it. You are a mirror right now, not a mechanic. The moment you start suggesting fixes, you've left the reflect-back loop and the user loses the ability to correct your understanding.

**Use AskUserQuestion for every reflect-back.** Don't just dump text — use the tool so the user has a clear, structured prompt to respond to. This creates a natural conversational rhythm.

**Don't ask generic questions.** Never ask "Can you tell me more?" or "What else is happening?" These put the burden on the user. Instead, make a specific guess and let them correct it. Wrong guesses that get corrected are more productive than vague questions.

**Explore code between rounds.** After each correction from the user, you now have better information. Use it to look at more specific parts of the codebase so your next reflect-back is even more precise. Each round should get sharper, not stay at the same level of vagueness.

**Handle the "just go" escape hatch.** If the user says something like "close enough, just go" or "you're overthinking this, plan it" — respect that. Produce the best problem statement you can with current understanding and let them move to planning. But note in the problem statement that the problem definition was approximate.

**This skill works for everything — not just bugs.** Feature requests, UX changes, performance issues, refactors — anything where the user's intent needs to be crystal clear before work begins. The reflect-back loop is the same regardless of category.
