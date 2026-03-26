# How to Trace an Existing Architecture

This is the most important phase of blueprint-first development. You're building a complete map of how an existing feature works — not from docs or memory, but from reading actual source code.

## What You're Producing

A layered data flow diagram that shows every step from entry point to final output. Each layer documents:
- **File path** with line numbers for key functions/types
- **Purpose** — one sentence
- **Data in** — what it receives, from where, in what type
- **Data out** — what it produces, where it goes, in what type
- **Configuration** — what config values affect behavior
- **Error handling** — how errors propagate, what fallbacks exist
- **Tests** — where tests live, what they cover

## How to Trace

### Start at the entry point

Find where the feature begins. For a model metadata pipeline, that's the config file or bundled catalog. For an auth flow, it's the login command. For a tool, it's the tool registration.

### Follow the data, not the files

Don't read files top to bottom. Follow the data flow function by function:
1. Find where data enters the system
2. Read the function that processes it — what does it call next?
3. Follow that call — what does IT call?
4. Continue until you reach the consumer (UI, API response, file output)

### Trace ALL paths, not just the happy path

The shortcuts you're trying to prevent hide in secondary paths:
- **Error paths** — what happens when the API call fails?
- **Cache paths** — is there a cache layer? What's the TTL? What triggers refresh?
- **Config override paths** — can the user override values? At what layer?
- **Fallback paths** — what happens with no auth? No network? Custom catalog?

These secondary paths are where workarounds get introduced, because they're easy to forget.

### Use the Explore agent

Spawn subagents for independent layers. Give each a clear mandate:
- "Trace the bundled catalog layer: where is models.json loaded, what type does it produce, who consumes it?"
- "Trace the cache layer: where is the cache file read/written, what's the TTL, what triggers refresh?"
- "Trace the display layer: where does the TUI status card get its data, what's the priority chain?"

### Document as you go

Don't wait until the end. Write each layer's documentation as you read it. This forces you to actually understand what you're reading, not just skim.

## How to Present

Use a visual data flow diagram. ASCII box diagrams work well:

```
┌───────────────────────────────────────────────────────────┐
│ LAYER 1: Bundled Catalog                                   │
│                                                           │
│ File: core/models.json (compiled via include_str!)         │
│ Loaded by: ModelsManager::load_remote_models_from_file()   │
│ Stored in: remote_models: RwLock<Vec<ModelInfo>>           │
│ Config: model_catalog overrides this entirely              │
└───────────────────────┬───────────────────────────────────┘
                        │
                        ▼
┌───────────────────────────────────────────────────────────┐
│ LAYER 2: Remote API Refresh                                │
│ ...                                                       │
└───────────────────────────────────────────────────────────┘
```

The exact format doesn't matter. What matters:
- Every layer is visible
- File paths are real and specific
- Data flow between layers is clear
- Config influence is documented at each step

## The Confirmation Gate

After presenting, ask: "Does this match your understanding? Anything missing?"

Wait for the user to confirm. They know the codebase and will catch gaps. If they spot something, go back and trace the missing part. Everything downstream depends on this being accurate.
