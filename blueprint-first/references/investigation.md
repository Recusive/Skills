# Investigation Workflow

Investigation answers a question about the codebase. It does NOT make changes. The deliverable is understanding — documented clearly enough that the next decision (build, fix, refactor, or do nothing) is well-informed.

## When to Investigate

- Before any other task type, when you don't understand the relevant code
- When the user asks "how does X work?"
- When a bug report is vague and you need to understand the system before diagnosing
- When you're evaluating whether a feature is feasible
- When you need to assess the impact of a proposed change
- When you're onboarding to an unfamiliar part of the codebase

## The Process

### Step 1: State the Question

Not "explore the codebase" — that's aimless. State a specific question:

- "How does the model metadata flow from config to the TUI status bar?"
- "What happens when the auth token expires during a streaming response?"
- "How are new CLI subcommands registered and wired?"
- "What would need to change to support a third model provider?"

A clear question focuses the investigation and tells you when you're done.

### Step 2: Find the Entry Point

Every investigation starts somewhere. Find the entry point for your question:

- For data flow questions → start where the data enters the system
- For "what happens when" → start where the user action or event triggers
- For "how is X registered" → start at the registration site
- For "what would need to change" → start at the current implementation

### Step 3: Trace the Flow

From the entry point, follow the execution path. Read actual code, not docs about code.

For each step in the flow, document:
- **File:line** — where you are
- **What happens here** — one sentence
- **What gets called next** — the next step in the flow
- **What could go wrong** — error paths, edge cases

Use the Explore agent for deep tracing if the flow crosses multiple crates or modules.

### Step 4: Document Findings

Present your findings as a structured document:

```markdown
## Question
[The specific question you were investigating]

## Summary
[2-3 sentence answer]

## Detailed Flow
[Step-by-step trace with file paths and line numbers]

## Key Observations
[Things that surprised you, potential issues, design decisions you noticed]

## Implications for [Next Task]
[If this investigation informs a feature/fix/refactor, what does it mean for that work?]
```

### Step 5: Present and Stop

Present findings to the user. Do NOT proceed to implementation. Investigation and implementation are separate decisions.

The user might say:
- "OK, now build it" → switch to the appropriate feature/fix workflow
- "Interesting, but let's investigate X further" → continue investigating
- "Good to know, let's not change anything" → done
- "That's different from what I expected — let me think" → wait

## Investigation Techniques

### Forward Trace
Start at the entry point, follow the execution forward to the output. Good for "how does data flow from A to B?"

### Backward Trace
Start at the symptom or output, trace backward to the source. Good for "where does this value come from?"

### Comparative Trace
Trace two similar features and compare. Good for "how does the GPT path differ from the Claude path?"

### Impact Analysis
Start at the proposed change point, trace outward to everything it affects. Good for "what would break if I changed this type?"

### Grep-and-Read
Search for a specific pattern, function name, or type across the codebase. Good for "who uses this?" or "where is this configured?"

## Investigation Gotchas

**Investigating without a question.** "Let me explore the codebase" produces aimless reading and no actionable output. Always start with a specific question.

**Reading docs instead of code.** Docs describe intent. Code describes reality. When they disagree, code wins. Always verify docs claims against actual implementation.

**Jumping to conclusions mid-investigation.** You trace 3 steps and think you see the answer. But step 4 has a cache layer that changes everything. Follow the trace to the end before concluding.

**Not documenting as you go.** You trace a 6-layer pipeline, then try to write it up from memory. You forget the file paths, mix up the order, and miss the cache layer. Document each step as you read it.

**Investigating more than you need.** The question was "how does auth token refresh work?" and you've now traced the entire auth system including registration, device code flow, and token storage. Stay focused on the question. Note tangential findings for later, don't follow them now.
