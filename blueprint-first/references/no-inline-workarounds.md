# No Inline Workarounds

Fix problems at the source, not at the consumer. This is the single most common architectural mistake in AI-generated code.

## The Principle

If consumer code needs special-case logic for your feature, your data pipeline is broken. Go back and fix the pipeline so the data arrives correct.

Every consumer-level workaround multiplies — there's never just one consumer. If the TUI needs a workaround, so does the app-server, so does the CLI, so does the test harness.

## Real Example

### The Problem
The TUI status card needs to show the correct context window for Claude models, but the metadata pipeline only produces correct values for GPT models.

### Bad (Consumer-Level Workaround)
```rust
// In tui/src/status/card.rs
fn get_context_window(model: &str, token_info: &TokenInfo) -> i64 {
    if model.starts_with("claude") {
        anthropic_model_context_window(model)  // inline lookup!
    } else {
        token_info.model_context_window.unwrap_or(config.model_context_window)
    }
}
```

Now the status card has provider-specific logic. When a third provider is added, someone needs to add another branch here. And in the footer. And in the app-server adapter. And in every other consumer.

### Good (Fix at the Source)
```rust
// In core/src/models_manager — the metadata pipeline
// Claude models get correct context_window from catalog + API refresh
// Same as GPT models do

// In tui/src/status/card.rs — NO CHANGES NEEDED
// The existing 2-level priority chain works for all models:
//   1. token_info.model_context_window (from TurnStartedEvent)
//   2. config.model_context_window (before first turn)
```

The TUI code doesn't even know Claude models exist. It just reads the data, which arrives correct from the pipeline.

## How to Detect Workarounds in Your Design

Ask these questions:
1. Does any consumer have an `if` branch for your feature specifically?
2. Does any consumer call a function that only exists for your feature?
3. Would removing your feature require changes in multiple consumers?
4. Does the display layer need to know which provider the data came from?

If YES to any: your data pipeline is incomplete. Fix the pipeline.

## The Debugging Rule

When the output is wrong in a consumer, trace backward:
1. Is the consumer reading the data correctly? (probably yes)
2. Is the data arriving correctly? (probably no)
3. Where in the pipeline does the data become wrong?
4. Fix it THERE.

Don't patch the consumer. Fix the source.
