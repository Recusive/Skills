# Never Hardcode

If a value could change, it doesn't belong as a literal in the code.

## Why This Matters

Hardcoded values create a hidden maintenance burden. They work today but break silently when the world changes — a new model releases, an API version bumps, a capability gets added. The person who has to update them may not know all the places to look.

## The Test

Ask: "If someone added a new variant (a new model, a new provider, a new tool type), would my code handle it automatically via data, or would they need to edit source code?"

The answer should be "via data."

## What to Never Hardcode

### Model Capabilities

**Bad:**
```rust
fn uses_adaptive_thinking(model: &str) -> bool {
    model.starts_with("claude-opus") || model == "claude-sonnet-4-6"
}
```

**Good:**
```rust
// Capabilities come from the catalog/API, stored in ModelInfo
if model_info.thinking_style == ThinkingStyle::Adaptive { ... }
```

When `claude-sonnet-4-7` releases with adaptive thinking, the catalog updates automatically. The hardcoded function needs manual patching.

### URLs, Endpoints, API Versions

**Bad:** `let url = "https://api.anthropic.com/v1/models";`

**Good:** Use the config system or a constant from a shared module. If the repo has a base URL config, use it.

### Feature Flags by Name

**Bad:** `if feature_name == "extended_thinking" { ... }`

**Good:** Use an enum, a capability field, or a feature struct. String matching is fragile and invisible to the type system.

### Default Values

**Bad:**
```rust
// In the display layer
let context_window = model_info.context_window.unwrap_or(200000);
```

**Good:**
```rust
// In the catalog/config layer where defaults are defined
// Display layer just reads the already-resolved value
let context_window = model_info.context_window;
```

Defaults belong in the layer where the data originates, not scattered across consumers.

## Where Defaults Belong

Follow the blueprint's pattern:
- **Bundled catalog** — default capability values for known models
- **Config layer** — user-overridable defaults
- **Factory/constructor** — structural defaults (cache TTL, retry count)
- **NEVER at the consumer** — the consumer should receive correct, resolved data
