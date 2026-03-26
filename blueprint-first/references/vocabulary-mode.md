# Vocabulary Mode — New Feature, No Direct Analog

Use this when there's no 1:1 analog to mirror. The feature is new — but the repo still has an engineering culture embedded in its code. A new feature that ignores that culture becomes a foreign body in the codebase.

The constraint isn't "mirror this specific pipeline." It's "this codebase has a way of doing things, and the new feature must be fluent in it."

## The Process

### Step 1: Shape Matching

Find 2-3 existing features with a similar structural shape. Not similar purpose — similar shape. The "shape" is the kind of component you're adding:

- Adding a new **panel**? Find how the terminal panel, browser panel, and vault panel were added.
- Adding a new **command**? Find how existing commands were registered.
- Adding a new **data pipeline**? Find how existing pipelines flow data from source to consumer.
- Adding a new **API endpoint**? Find how existing endpoints are defined, routed, and tested.

For each example, document:
- What files were involved
- What types were created
- What registrations or wiring were needed
- How the feature connects to the rest of the system

Then extract the **common skeleton** — not what's unique to each feature, but what's shared across all of them. That skeleton is the repo's pattern for "how to add a thing of this shape."

**Present to user:** "Here are the 2-3 examples I found and the common pattern I extracted. Does this look right?"

**Wait for confirmation.**

### Step 2: Integration Point Mapping

A new feature doesn't exist in isolation. It touches existing systems. Identify every place the new feature needs to connect BEFORE designing the feature itself.

For each integration point:
1. Read the actual interface — the types, the trait, the registration function, the event system
2. Document what the existing system expects from a new consumer
3. Note any constraints (required types, expected lifecycle, thread safety requirements)

These are **non-negotiable constraints** — the new feature must fit into these interfaces, not invent its own.

Example for a "Vault" (markdown notes) feature:
- **Panel system** — how does it get displayed? What trait/interface must it implement?
- **State store** — how does it persist? Zustand? Rust RwLock? SQLite?
- **Agent layer** — can the agent read/write notes? What tool registration is needed?
- **Keybinding system** — how does the user open it? What's the registration pattern?
- **File system** — where are notes stored? What directory conventions exist?

### Step 3: Convention Audit

Read `file-structure.md` for the detailed guide. Additionally, audit the 3 most recently added modules (check git log) for:

- Where is the module declared? `mod.rs` or named file?
- How are types exported? `pub(crate)` + `pub use` re-exports?
- How are errors defined? `thiserror` with `#[derive(Debug, Error)]`?
- How are tests structured? Sibling `_tests.rs` or inline `#[cfg(test)]`?
- Where is the wiring/registration code?
- Are there builder patterns?
- How is async handled at boundaries? Channels, callbacks, async/await?
- How are optional features handled? Feature flags, Option types, trait objects?

### Step 4: Top-Down Design

Now design. Start from the **user's action**, not the internals.

"The user opens the Vault panel. That means the panel system needs a VaultPanel registered. The panel renders markdown, so it needs a content state. The content state needs to load from somewhere, so it needs a storage layer. The agent needs to read notes, so the storage layer needs to be accessible from the agent context."

Each sentence maps to a layer. Each layer connects to an integration point from Step 2. Each layer follows the conventions from Step 3. Each layer cites a precedent from the shape matching in Step 1.

Present as a layer diagram with file paths and types. Every layer must cite an existing precedent in this repo.

### Step 5: Engineering Stages

Read `engineering-stages.md` and work through every applicable stage. This is where you design the data model, state lifecycle, error cases, concurrency model, API surface, config, dependencies, tests, migration, performance, and security.

### Step 6: The Alien Code Test

Before finalizing, compare one key file from the new feature design against one analogous file from an existing feature. They should look like they belong in the same codebase — same patterns, same conventions, same level of abstraction.

If they don't match, identify what's different and fix it. Common mismatches:
- Different error handling style
- Different visibility (pub vs pub(crate))
- Different import organization
- Different naming conventions
- Different test structure
- Different level of abstraction (too high-level or too granular)

### Step 7: Write the Plan

Read `plan-template.md` for the document structure. The plan should include:
- The shape-matching analysis (what precedents you found)
- The integration point map (what existing systems this touches)
- The convention audit results (how this repo does things)
- The full design (layer by layer, each citing a precedent)
- All applicable engineering stages

### Step 8: User Review

Walk user through the design. Wait for approval.
