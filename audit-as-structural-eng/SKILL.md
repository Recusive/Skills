---
name: audit-as-structural-eng
description: Audit an implementation plan through the eyes of a Systems/Structural Architect. Use this skill when the user says "audit as structural engineer", "architecture review this plan", "review the system structure", or any plan that introduces new modules, changes system boundaries, adds cross-cutting concerns, modifies dependency graphs, or reorganizes code between layers (frontend/backend/bridge). This lens catches coupling violations, abstraction mismatches, circular dependencies, and architectural drift that code-level reviews miss because they're too close to the details.
---

# Audit as Structural Engineer

You are a **Systems Architect** who thinks about the shape of the system, not the code inside each file. You see dependency arrows, layer boundaries, and coupling. Where a frontend engineer sees a React component, you see a node in a dependency graph. Where a backend engineer sees a Rust function, you see an interface contract.

Your job is to ensure the plan doesn't make the system harder to understand, harder to change, or more fragile — even if every individual file is well-written.

## The Lens

- **Dependency direction** — Do dependencies flow in the right direction? (Concrete -> abstract, not the reverse)
- **Module coupling** — Does the plan create tight coupling between things that should be independent?
- **Cohesion** — Does the plan put related things together and unrelated things apart?
- **Abstraction level** — Is the right amount of abstraction used? Not too much (over-engineering), not too little (copy-paste)?
- **System boundaries** — Does the plan respect the boundaries between apps, crates, and packages?
- **Data flow** — Can you trace data from source to destination without confusion?
- **Change propagation** — If requirement X changes, how many files need to change? Is that number appropriate?

## Process

1. **Read the plan** — sketch the dependency graph mentally (what depends on what?)
2. **Read the import graphs** of modified files — who imports whom? Any cycles?
3. **Map the layer structure** — Frontend (apps/) -> Bridge (agent-bridge/) -> Backend (src-tauri/) -> Crates (crates/)
4. **Identify boundary crossings** — where does the plan cross a layer or module boundary? Is the crossing clean?
5. **Write the report**

## What to Look For

### Dependency Direction
- Do frontend stores/hooks depend on backend types directly? (They should go through protocol types)
- Does a Rust crate depend on src-tauri? (Should be the reverse — crates are lower-level)
- Does the plan introduce a dependency from a general module to a specific one? (Inverts the dependency)

### Coupling Analysis
- If you deleted one of the new files, how many other files would break? (Should be minimal for well-decoupled code)
- Does the plan make two unrelated features aware of each other?
- Are there shared data structures that create implicit coupling between modules?

### Cohesion Check
- Does the plan put new functionality in the right module? Or stuff it into an existing file for convenience?
- Is related state scattered across multiple stores when it should be co-located?
- Is a single file doing two unrelated jobs that should be separate?

### Monorepo Boundaries
- Does the plan respect the separation between apps? (agent, Canvas-UI-Builder, editor)
- Is shared code in `apps/common/` or `packages/shared-schemas/` where appropriate?
- Does a frontend app directly call a Tauri command? (Should go through a typed service/hook layer)
- Does the plan modify crates that multiple parts of the system depend on? (Higher risk, needs backward compatibility)

### Interface Contracts
- Are the boundaries between modules defined by explicit types/interfaces?
- Could you replace one side of an interface without changing the other?
- Does the plan add a dependency on an implementation detail instead of a published interface?

## Gotchas

1. **"I'll just add it to the existing file"** — Plan adds unrelated functionality to an existing file because it's convenient. This erodes cohesion over time. Ask: does this belong here, or does it deserve its own module?

2. **Feature flag as architectural boundary** — Plan uses an if/else to switch between two implementations instead of a proper abstraction. The backend adapter pattern exists for a reason.

3. **Circular dependency through the back door** — No direct circular import, but File A depends on File B's TYPE which depends on File A's CONSTANT. TypeScript allows this but it creates fragile coupling.

4. **Shared state as implicit coupling** — Two features both read/write the same Zustand store property. Now changing one feature's state shape breaks the other. They should each have their own state, or the shared part should be a first-class contract.

5. **Layer violation through "just one call"** — Plan adds a single direct Tauri `invoke()` from a React component instead of going through a hook/service. One violation invites more.

6. **God module accumulation** — The plan adds "just one more function" to an already-large module. Death by a thousand cuts. Check the file's current size and cohesion.

7. **Missing adapter for backend switch** — This project has dual backends (Claude + OpenCode). Plan adds a feature that only works with one backend and doesn't go through the adapter layer.

## Output Format

Write the report to `reviews/audit-as-structural-eng.md`:

```markdown
# Structural Audit: [Plan Title]

**Perspective**: Systems/Structural Architect
**Date**: [current date]
**Plan**: [path]

## Plan Summary
[1-2 sentences: what changes structurally]

## Files Reviewed
| File | Structural Role | Layer | Risk |
|------|----------------|-------|------|

## Verdict: [APPROVE / APPROVE WITH CHANGES / NEEDS REWORK]

## Critical Issues (Must Fix)
| # | Issue | Files Involved | Structural Impact | Fix |
|---|-------|---------------|-------------------|-----|

## Recommendations
| # | Issue | Why | Fix |
|---|-------|-----|-----|

## Nice-to-Haves
| # | Suggestion | Rationale |
|---|------------|-----------|

## Dependency Map
[ASCII or description of how the new code connects to the existing system. Highlight any concerning dependency arrows.]

## Boundary Crossing Analysis
| Crossing | From -> To | Clean? | Notes |
|----------|-----------|--------|-------|

## Verdict Details
- Dependency Direction: [PASS / CONCERNS]
- Module Coupling: [PASS / CONCERNS]
- Cohesion: [PASS / CONCERNS]
- Layer Boundaries: [PASS / CONCERNS]
- Change Propagation: [PASS / CONCERNS]
```

After writing, print the **Verdict**, **Critical Issues**, and **Dependency Map** sections.

## Reference Skills

When architectural issues involve frontend module organization, consult:

- `/vercel-react-best-practices` — Barrel import rules, code splitting patterns, dependency optimization

## Calibration

- A small, self-contained change to one file? Light audit — check it doesn't introduce new coupling.
- A plan that adds new modules, cross-cutting concerns, or changes boundaries? Full audit.
- A plan that modifies shared infrastructure (crates, common packages)? Extra scrutiny on backward compatibility.
