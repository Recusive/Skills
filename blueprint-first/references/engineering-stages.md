# The 16 Engineering Stages

A real engineer mentally runs through this full list for every feature and consciously decides which stages apply. The ones that apply get designed explicitly. The ones that don't get noted as "N/A — [reason]" so it's a conscious decision, not an oversight.

Not every feature needs all 16. A new UI panel probably doesn't need a migration strategy. A new data pipeline probably doesn't need a security audit. But skipping a stage because you forgot about it is very different from skipping it because you decided it doesn't apply.

---

## Stage 1: Data Modeling

Before any logic — what are the actual types?

- What structs get created? What fields, what types?
- What's optional vs required? What has defaults?
- What enums are needed? What variants?
- What goes in persistent storage vs in memory vs in config?
- In Rust: what's owned vs borrowed? What's Clone? What needs Arc?
- What derive sets? (Check repo conventions — config types, protocol types, and API types often have different derives)

Engineers spend more time here than anywhere else because getting the data model wrong cascades into everything downstream.

**Key question:** If you showed just the type definitions to another engineer, could they guess what the feature does?

---

## Stage 2: State Ownership and Lifecycle

Where does this feature's state live? The answers dictate the architecture.

- **Who creates it?** At app startup? On first use? Lazily?
- **Who owns it?** A specific service? Shared via Arc? Global singleton?
- **Who can read it?** Everyone? Only the owning module? Through an interface?
- **Who can mutate it?** Only the owner? Through a message queue? Via RwLock?
- **When is it destroyed?** App shutdown? Session end? Panel close?
- **Can it be reset?** What happens to in-flight operations?

In a VS Code fork / Tauri app: what lives on the Rust side vs the React side? What crosses IPC? What's in Zustand vs what's in a Rust RwLock?

---

## Stage 3: Error Enumeration

Not "how does the repo handle errors" (that's conventions). What specifically can go wrong with THIS feature?

Enumerate every failure:
- Network down during API call
- Malformed input from user
- Empty/missing state (first run)
- Race conditions (two operations on same resource)
- Partial failures (3 of 5 items succeed)
- Permission denied (file system, API auth)
- Timeout (slow network, large payload)
- Invalid state transitions

For each failure: does the feature retry? Degrade gracefully? Show an error? Fall back to defaults?

Error paths often change the data model — you realize you need a `Result` where you assumed infallible, or you need a retry queue, or you need a degraded-mode fallback.

---

## Stage 4: Concurrency Model

Who's calling what from which thread?

- What runs on the main thread vs background tasks?
- What async runtime is in use? (Tokio, Bun, browser event loop)
- What locks are held and for how long?
- Can this deadlock against anything existing?
- What's the cancellation story? User navigates away mid-operation?
- Are there race conditions between the new feature and existing features?

In a multi-process architecture (Electron + Rust + Sidecar):
- Which process does each operation run in?
- What's the IPC serialization cost?
- Can messages arrive out of order?

---

## Stage 5: Public API Surface

What does this feature expose to the rest of the codebase? Not the internal implementation — the **contract**.

- What functions/methods are public?
- What traits does it implement?
- What events does it emit?
- What commands does it register? (Tauri commands, CLI subcommands)
- What IPC messages does it send/receive?
- What state does it expose to the UI?

Define this boundary explicitly. Once other code depends on it, changing it is expensive. Keep the surface minimal — expose only what consumers actually need.

---

## Stage 6: Config Design

What new configuration does this feature introduce?

- What settings does the user control?
- Where do they live in the config hierarchy? (global, per-provider, per-project)
- What are the defaults? (Must preserve current behavior for existing users)
- What happens if the config is missing? (First run, upgrade from older version)
- What's the migration story if you change the config shape later?
- What validation is needed?

Run `just write-config-schema` (or equivalent) after any config changes.

---

## Stage 7: Dependency Audit

Does this feature need new external dependencies?

- New crates / npm packages / Python packages?
- What's the binary size impact?
- What's the compile time impact?
- Are there security advisories on the dependency?
- Does it conflict with existing dependencies? (Version mismatches, runtime conflicts)
- Does it need new system capabilities / permissions?

In workspace projects: add to `[workspace.dependencies]`, not per-crate. Update lockfiles.

---

## Stage 8: Top-Down Design

Start from the user's action, trace downward through layers:

1. **User action** — what do they do?
2. **UI layer** — what do they see? What state drives the display?
3. **Command/handler layer** — what processes the action?
4. **Business logic** — what transformations happen?
5. **Data layer** — what's read/written?
6. **External systems** — what APIs/services are called?

Each layer must cite an existing precedent in this repo. If a layer has no precedent, it's either genuinely new (document why) or you haven't looked hard enough.

---

## Stage 9: Testing Strategy

Not "where do tests go" but WHAT gets tested and HOW.

- **Unit tests** — data model transformations, pure logic, error cases
- **Integration tests** — wiring between layers, IPC, actual file/network operations
- **Snapshot tests** — UI output, serialization format (if TUI: use insta)
- **Mock strategy** — what needs mocking? External APIs (wiremock), file system (tempdir), IPC?
- **Key scenarios** — the 5-10 scenarios that prove the feature works end to end
- **Regression tests** — what existing behavior must not break?

Use the repo's existing test utilities: `TestCodexBuilder`, `mount_sse_once`, `pretty_assertions`, etc.

---

## Stage 10: Migration and Rollout

If this touches existing state, config, or persisted data:

- What happens on upgrade from a version without this feature?
- Is there an automatic migration? What if it fails halfway?
- What about downgrade — does the app still work with new data format on old version?
- Does the user need to do anything manually?
- Are there any one-time setup steps?

---

## Stage 11: Performance Budget

- Does this add startup latency? (Cold path vs hot path)
- Memory overhead? (Persistent state, caches, buffers)
- New network calls on hot paths? (Every keystroke? Every agent turn?)
- CPU-intensive operations? (Parsing, rendering, cryptography)
- What's the expected data size? (10 items or 10,000?)

If the feature runs on every keystroke or every agent turn, performance is critical. If it runs once on startup, it probably isn't. Know which category you're in.

---

## Stage 12: Security Surface

- Does this introduce new inputs from untrusted sources?
- New file system access? (Read, write, delete)
- New network endpoints? (Outbound calls, servers)
- New commands the frontend/agent can invoke?
- New data that crosses trust boundaries? (User input → system command)
- Can the agent access this feature? What's the threat model?

Every new surface is a potential vulnerability. Enumerate them even if they're low risk.

---

## Stage 13: Integration Point Mapping

(Usually done during vocabulary-mode Step 2, but also relevant in blueprint mode)

List every existing system the new feature touches. For each:
- What's the interface? (Trait, function signature, event type)
- What does it expect from a new consumer?
- What constraints does it impose? (Required types, thread safety, lifecycle)

---

## Stage 14: Convention Audit

(Usually done during vocabulary-mode Step 3, shared with `file-structure.md`)

Check the 3 most recently added modules for: module location, type exports, error definitions, test structure, wiring patterns, builder patterns, async boundaries.

---

## Stage 15: Shape Matching

(Vocabulary mode only — find structural precedents from multiple examples)

---

## Stage 16: Alien Code Test

Before finalizing: if someone opened a file from this new feature and a file from an existing feature side by side, would they look like the same team wrote them?

Same error handling. Same naming. Same abstraction level. Same config approach. Same test structure.

If they don't match, something's wrong. Fix it before the plan is finalized.
