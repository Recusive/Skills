# How to Create a Proper System Design

You've traced the reference architecture. Now design the new feature as a mirror.

## The Core Rule

For every layer in the reference architecture, design the corresponding layer for the new feature. No exceptions. If the reference has 6 layers, your design has 6 layers.

## Layer-by-Layer Design

For each layer:

1. **Start with "this mirrors [Reference Layer N]"** — make the connection explicit
2. **Document what's identical** — reuse existing code paths wherever possible. If the reference uses `apply_remote_models()` to merge data, your design uses the same function.
3. **Document what differs** — and provide a TECHNICAL justification. Not "it's simpler this way" but "the Anthropic API returns `max_input_tokens` instead of `context_window`"
4. **List new types/structs** — follow naming patterns of existing types. If existing types are `OpenAiModelInfo`, yours is `AnthropicModelInfo`. If they derive `Serialize, Deserialize, Debug, Clone`, yours does too.
5. **Specify file location** — following the repo's organization. If the OpenAI client is in `core/src/models_manager/manager.rs`, the Anthropic integration goes in the same directory.

## Side-by-Side Presentation

Present both pipelines together so deviations are immediately visible:

```
REFERENCE (GPT)                    NEW (Claude)
────────────────                   ────────────────
Layer 1: Bundled Catalog           Layer 1: Bundled Catalog
  models.json with GPT entries       models.json + Claude entries
  IDENTICAL PATH                     (same loader, same type)

Layer 2: Remote API Refresh        Layer 2: Remote API Refresh
  GET /models (OpenAI)               GET /v1/models (Anthropic)
  DIFFERS: different endpoint,       New: AnthropicModelsClient
  different response shape           Mapping layer required

Layer 3: Cache                     Layer 3: Per-Provider Cache
  models_cache.json                  models_cache_anthropic.json
  DIFFERS: separate file per        New: for_anthropic() constructor
  provider (no ETag for Anthropic)
```

## The Deviation Test

For every place your design differs from the blueprint:

**Ask:** "Is this because the new feature genuinely requires a different approach, or because it would be faster to implement?"

Only the first reason is valid.

**Valid deviations:**
- Different API pagination (cursor vs offset)
- Different auth mechanism (API key vs OAuth)
- Different response shape requiring a mapping layer
- Missing API feature (no ETag → no conditional refresh)

**Invalid deviations:**
- "We can hardcode this for now"
- "The cache layer isn't needed yet"
- "We'll add config support later"
- "This is simpler as an inline function"
- "Let's skip the migration path"

## Integration Architecture

After the layer-by-layer design, document how the new feature integrates with existing systems:

- **Constructor wiring** — who creates the new objects and passes them where?
- **Shared state** — does the new feature read/write any shared state (RwLocks, caches)?
- **Config resolution** — how do global, per-provider, and per-entity configs interact?
- **Dependency injection** — how is the new provider made available to consumers?

## Quality Check

Before declaring the design complete:
- Same number of layers as the reference ✓
- Every deviation has a technical justification ✓
- File locations follow repo conventions ✓
- New type names follow existing naming patterns ✓
- Integration points are explicit ✓
- No "add later" or "for v1" language ✓
