# Blueprint Mode — Mirror a 1:1 Analog

Use this when a direct analog exists: "add Claude model support" when GPT support exists, "add Anthropic auth" when OpenAI auth exists.

## The Process

### Phase 1: Identify the Blueprint

Find the closest existing analog. Ask the user if unclear: "What's the closest existing feature I should study?"

Confirm before proceeding: "The reference implementation is [X]. I'm going to trace its full pipeline before designing anything."

### Phase 2: Trace the Full Pipeline

Read `tracing-architecture.md` for the deep guide.

Read actual source code. Document every layer from entry point to final output. Present a visual data flow diagram with file paths, types, and config at each step. **Wait for user confirmation** before proceeding.

### Phase 3: Extract Behavioral Contracts

Read `behavioral-contracts.md` for the deep guide.

Document invariants that must be preserved: config semantics, auth gates, fallback chains, shared state. Each contract: what, where (file:line), why it matters, how to preserve it.

### Phase 4: Mirror the Blueprint

Read `system-design.md` for the deep guide.

For every layer in the reference, design the corresponding layer. Present both pipelines side-by-side. Every deviation needs a technical justification — never a speed justification. If your design has fewer layers, something is wrong.

### Phase 5: Anti-Shortcut Validation

Read `anti-shortcut-checklist.md` for the full checklist.

Review against hardcoded values, missing layers, consumer-level workarounds, incomplete integration, and infrastructure reuse. Fix all failures before proceeding.

### Phase 6: Engineering Stages

Read `engineering-stages.md` and run through the applicable stages for this feature.

### Phase 7: Write the Plan

Read `plan-template.md` for the document structure.

### Phase 8: User Review

Walk user through key decisions. Wait for approval. Save to project's plan directory.
