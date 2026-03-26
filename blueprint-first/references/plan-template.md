# Plan Document Template

Use this template when writing the plan in Phase 6. Adapt the sections to fit the feature — not every section applies to every plan, but include a section rather than omitting it unless it's genuinely irrelevant.

The anthropic-model-metadata-pipeline plan (in this repo's `docs/tracked/todo/`) is the gold standard. When in doubt about depth or quality, reference that plan.

---

## Template

```markdown
# [Feature Name] — Proper Architecture

## Context

[What exists today. What's wrong with it or what's missing. State the guiding principle clearly.]

**Reference:** [Path to the reference implementation — the blueprint you traced]

---

## System Design — Complete Data Flow

### [Reference Name] (existing, reference architecture)

[Full data flow diagram from Phase 2. Every layer with:
- File path
- Purpose
- Key types
- Config that affects behavior
- How data flows to the next layer]

### [New Feature Name] (target — mirrors [Reference] exactly)

[Full data flow diagram from Phase 4. Same structure as above.
Where a layer mirrors the blueprint identically, say so.
Where it differs, call out the deviation with technical justification.]

### [Integration Architecture] (if the new feature connects to existing systems)

[How the new and existing components wire together.
Constructor signatures, shared state, dependency injection patterns.
Show how the new feature plugs into existing infrastructure
rather than creating parallel systems.]

---

## [External API Reference] (if applicable)

[For features that talk to external APIs:]
- Endpoint URL and method
- Request/response format with actual JSON examples
- Auth requirements and quirks
- Pagination mechanism
- Error responses and their meaning
- Known issues (e.g., "OAuth returns 401, API key works")

---

## Behavioral Contracts to Preserve

[From Phase 3. For each contract:]

### [Contract Name]
- **What:** The behavior that must be preserved
- **Where:** `file/path.rs:42` — the code that implements it
- **Why:** What breaks if violated
- **How:** How your design preserves it

---

## Implementation Phases

### Phase N: [Layer Name] ([foundation/core/consumer])

**File:** `path/to/file.rs`

[What to add or modify. Include:
- Type definitions with derive sets
- Function signatures
- Key logic decisions
- Serde attributes and naming conventions
- Enough detail that the implementer doesn't guess]

**Tests:** `path/to/tests.rs` — [specific scenarios to test]

---

## Phase Dependencies

[ASCII or text dependency graph. Show:
- Which phases can run in parallel
- Which phases gate on others
- The critical path]

```
Phase 1 (types)     ─┐
Phase 2 (catalog)    ─┤
Phase 3 (client)     ─┤─→ Phase 4 (mapping) ─→ Phase 6 (manager)
Phase 5 (cache)      ─┘                       ─→ Phase 7 (wiring)
Phase 8 (config)     ── independent
Phase 9 (cleanup)    ── after Phase 7
```

---

## Config File Example

[What the user-facing configuration looks like after implementation.
Show actual TOML/JSON/YAML with comments explaining each field.]

---

## Verification

### Automated
[Test commands for each phase, in order. Include the crate/package name.]
```bash
cargo test -p orbit-code-protocol           # Phase 1
cargo test -p orbit-code-core --lib         # Phases 4-7
just write-config-schema                    # Phase 8
just fmt
```

### Manual
[Step-by-step scenarios. Number them. Include expected output.]
1. Start the app, select [model], run `/status` → should show [X]
2. Switch to [other model], run `/status` → should show [Y]
3. Disconnect network → bundled catalog still works
4. Set [config value] → verify [behavior]

### Test Files to Add/Update
[Explicit list of every test file that needs changes, with what to test]

---

## Files Changed Summary

| File | Action | Phase |
|------|--------|-------|
| `path/to/types.rs` | Modify — add capability fields | 1 |
| `path/to/client.rs` | **New** — API client | 3 |
| `path/to/client_tests.rs` | **New** — tests | 3 |
| `path/to/existing.rs` | Modify — dual-provider support | 6 |
```

---

## Quality Checklist

Before presenting the plan to the user, verify:

- [ ] Every file path in the plan exists in the current codebase (for modified files) or has a clear parent directory (for new files)
- [ ] Every type name matches actual codebase types (not invented names)
- [ ] Every behavioral contract has a real file:line reference
- [ ] The reference architecture diagram was traced from actual code, not reconstructed from memory
- [ ] The new design has the same number of layers as the reference
- [ ] The anti-shortcut checklist from Phase 5 passes
- [ ] The verification section includes both automated and manual steps
- [ ] The files changed summary is complete (no "and other files" hand-waving)
- [ ] A senior engineer unfamiliar with this conversation could implement correctly from this document alone
