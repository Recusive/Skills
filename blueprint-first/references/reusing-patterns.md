# Reusing Existing Patterns and Infrastructure

Before writing anything new, search for existing infrastructure that does what you need.

## Why This Matters

Large codebases have shared utilities, builders, factories, and helpers that took real engineering effort to build — error handling, edge cases, concurrency, testing. Using them means your code inherits all that work. Writing parallel infrastructure means maintaining two systems that do the same thing.

## What to Look For

Before writing any new code, check if the repo already has:

### Shared Clients & Transport
- HTTP clients (with retry, auth headers, timeout)
- API clients for specific services
- Transport abstractions

### Infrastructure
- Cache managers (read, write, TTL, migration)
- Config readers (TOML, JSON, env var layering)
- Auth helpers (token refresh, credential storage)
- Secret management (keyring, encrypted storage)

### Test Utilities
- Test builders (e.g., `TestCodexBuilder` with fluent API)
- HTTP mocking helpers (e.g., `mount_sse_once`, `ResponseMock`)
- Fixture/resource loading (e.g., `find_resource!`)
- Common assertions (e.g., `pretty_assertions::assert_eq!`)

### Error Types
- Crate-level error enums
- Error conversion traits (`From` impls)
- Domain-specific error methods (`.is_retryable()`)

### Serialization Patterns
- Derive sets for different type categories
- `rename_all` conventions by context
- TypeScript export attributes

## How to Find Them

1. **Grep for similar functionality.** If you need caching, grep for `cache`. If you need HTTP, grep for `HttpTransport` or `reqwest`.
2. **Check the crate's `lib.rs`** — what's publicly exported? Those are the intended reuse points.
3. **Check `tests/common/`** — shared test utilities live here.
4. **Read CLAUDE.md conventions** — they often list specific utilities and patterns.
5. **Look at the closest analog** — if GPT models use `ModelsClient`, your Anthropic version should mirror its structure.

## Extending vs Creating

**Prefer extending** existing infrastructure:
- Add a new constructor to the existing cache manager → `for_anthropic(home, ttl)`
- Add a new method to the existing auth manager → `auth_cached_for_provider()`
- Add new fields to existing types → `ModelInfo` gains capability fields

**Only create new** when the functionality is genuinely different:
- A new API client for a different service's API shape → `AnthropicModelsClient`
- A new mapping layer for a different response format → `anthropic_mapping.rs`

## Red Flags

- You're writing a `new_cache()` function when `ModelsCacheManager` already exists
- You're writing HTTP retry logic when the existing client has it
- You're writing test helper functions that duplicate `core_test_support`
- You're defining a new error enum in a crate that already has one
- You're importing `reqwest` directly when the repo has a transport abstraction
