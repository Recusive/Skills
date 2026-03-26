# How to Extract and Preserve Behavioral Contracts

Behavioral contracts are the invariants that work correctly today and must continue working after your changes. They're the guardrails that prevent your new feature from breaking existing functionality.

## What to Look For

### Configuration Semantics
How does configuration flow through the system? The exact behavior matters:
- **Direct assign** — `model.context_window = config.model_context_window` (the user's config is trusted)
- **Capping** — `min(model.context_window, config.model_context_window)` (never exceeds a limit)
- **Priority chain** — token_info > config > catalog (which source wins?)

Get this wrong and users see incorrect values everywhere.

### Auth-Gated Features
Which auth modes enable which behaviors?
- "OpenAI model fetch requires `auth_mode == Chatgpt`"
- "Anthropic model fetch requires `auth_cached_for_provider(Anthropic)`"
- "Custom catalog mode disables ALL remote fetch"

If you add a new provider, its fetch must respect the same gating patterns.

### Fallback Chains
What happens when things fail?
- Network down → bundled catalog still works
- API returns 401 → fall back to bundled, don't crash
- No auth configured → use bundled catalog only
- Cache expired but API unreachable → use stale cache? Or bundled?

Document each failure scenario and what the current behavior is.

### Shared State
Who reads and writes shared state?
- "remote_models RwLock is written by the refresh task and read by get_model_info()"
- "Both OpenAI and Anthropic fetchers write to the same Vec<ModelInfo>"

If your new feature writes to shared state, you need to understand every reader.

### Backward Compatibility
What existing data formats must continue to work?
- "models_cache.json already exists on users' machines — migrating to models_cache_openai.json needs a fallback path"
- "ModelInfo has existing serialized instances in app-server responses — new fields need #[serde(default)]"

## How to Document

For each contract:

```markdown
### [Contract Name]
- **What:** Custom catalog mode disables ALL remote refresh
- **Where:** core/src/models_manager/manager.rs:142
- **Why:** When users provide their own catalog, it's authoritative.
  Running remote fetch would silently override their choices.
- **How to preserve:** Check CatalogMode before running any fetcher —
  both OpenAI and Anthropic. Neither should run in Custom mode.
```

## Common Mistakes

**Violating config override semantics.** The reference uses direct assign for config overrides. You introduce `min()` capping "for safety." Now config values that worked before produce different results and the user can't set a high context window.

**Breaking the fallback chain.** The reference falls back to bundled catalog on API failure. Your new provider panics on 401 instead of falling back. Now users without API keys can't use the tool.

**Forgetting shared state.** You add a new fetcher that writes to the shared model list, but you don't consider that the existing fetcher might be running concurrently. Race condition.

**Ignoring migration.** You rename a cache file but don't check for the old name on startup. Users who upgrade lose their cached data and have to re-fetch.
