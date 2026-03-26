# Anti-Shortcut Checklist

Run this checklist before writing the final plan. Every item represents a real failure mode observed in AI-generated plans. Be honest — a shortcut caught here saves hours of rework during implementation.

## Hardcoded Values

- [ ] Uses configuration/catalog where the blueprint does (no magic strings)
- [ ] Capabilities come from data, not name matching (no `if model.starts_with("claude")`)
- [ ] New enum variants/types follow existing naming patterns
- [ ] Default values live in the same layer as the blueprint's defaults (config, catalog, or constructor — not inline at the consumer)
- [ ] URLs, endpoints, and API versions come from config or constants, not string literals

## Missing Layers

- [ ] Same number of pipeline layers as the blueprint
- [ ] Cache layer exists if the blueprint has one
- [ ] Fallback/error path exists if the blueprint has one
- [ ] Migration path for existing data if applicable
- [ ] Config override layer exists if the blueprint has one
- [ ] Validation/mapping layer exists if the data shape differs from the internal type

## Consumer-Level Workarounds

- [ ] No special-case logic in consumers (UI, API, CLI) for the new feature
- [ ] Data fixed at the source, not patched at the display layer
- [ ] Existing display/status code works WITHOUT changes (data comes correct from the pipeline)
- [ ] No `if provider == "anthropic"` guards in consumer code
- [ ] No new utility functions in the consumer that should live in the data layer

## Incomplete Integration

- [ ] Flows through same resolution/override/display paths
- [ ] Configuration works identically (global → per-provider → per-entity)
- [ ] Existing tests pass without modification
- [ ] Schema regeneration commands documented if types changed
- [ ] Both tui/ and tui_app_server/ updated if either is touched (mirror convention)
- [ ] App-server protocol updated if events carry new data

## Infrastructure Reuse

- [ ] Extends existing infrastructure, not creating parallel systems
- [ ] Shared utilities (cache manager, HTTP client, auth, test helpers) reused
- [ ] New constructors/factories mirror existing ones
- [ ] Test setup uses existing builders (TestCodexBuilder, wiremock helpers)
- [ ] Error types extend existing crate errors, not new error enums

## Plan Document Completeness

- [ ] Every file path is real (exists in current codebase for modifications)
- [ ] Every type name matches actual codebase types
- [ ] Behavioral contracts have file:line references
- [ ] Phase dependencies are documented (what can parallelize)
- [ ] Verification section has both automated commands and manual scenarios
- [ ] Files changed summary is complete (no "and other files" hand-waving)

## If Any Check Fails

Fix the design. Don't proceed with a known shortcut. The cost of fixing it now (minutes of rethinking) is 100x less than fixing it during implementation (hours of rework and frustrated debugging).
