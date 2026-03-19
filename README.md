# Skills

Agent skills for [Orbit](https://github.com/Recusive), Claude Code, Cursor, Codex, and other AI coding assistants.

## Install

```bash
# Install all skills
npx skills add Recusive/Skills

# Install a specific skill
npx skills add Recusive/Skills --skill problem-clarifier
```

## Available Skills

| Skill | Description |
|-------|-------------|
| **[component-test](./component-test)** | Generate production-grade Vitest tests from HTML elements captured in DevTools |
| **[fazxes](./fazxes)** | Production scope gatekeeper — catches buried scope reductions before plans reach audit |
| **[mosaic](./mosaic)** | Create in-app stress tests that drive the real app pipeline from browser DevTools — no mocks |
| **[problem-clarifier](./problem-clarifier)** | Clarify problems and bugs through a reflect-back interview before planning |
| **[stress-test](./stress-test)** | In-app stress tests via DevTools console — real state, real APIs, real UI |
| **[test-engineer](./test-engineer)** | Write meaningful tests for TypeScript/React (Vitest) and Rust (cargo test) |
| **[vercel-react-best-practices](./vercel-react-best-practices)** | React and Next.js performance optimization guidelines from Vercel Engineering |
| **[web-design-guidelines](./web-design-guidelines)** | Review UI code for Web Interface Guidelines compliance and accessibility |
| | |
| **Orbit Audit Suite** | **10 expert audit lenses + orchestrator** |
| **[audit-as-design-eng](./audit-as-design-eng)** | Audit through the eyes of a Design Engineer — visual hierarchy, tokens, dark mode, states, animation |
| **[audit-as-ux-eng](./audit-as-ux-eng)** | Audit through the eyes of a UX Engineer — user journeys, mental models, discoverability, error recovery |
| **[audit-as-frontend-eng](./audit-as-frontend-eng)** | Audit through the eyes of a Frontend Engineer — React patterns, Zustand, type safety, re-renders, bundle |
| **[audit-as-backend-eng](./audit-as-backend-eng)** | Audit through the eyes of a Backend Engineer — Rust error handling, concurrency, resource lifecycle, IPC |
| **[audit-as-structural-eng](./audit-as-structural-eng)** | Audit through the eyes of a Structural Architect — coupling, dependencies, layer violations, abstractions |
| **[audit-as-repo-maintainer](./audit-as-repo-maintainer)** | Audit through the eyes of a Repo Maintainer — conventions, barrel exports, naming, code reuse, lint |
| **[audit-as-prod-readiness](./audit-as-prod-readiness)** | Audit for Production Readiness — E2E flow, env parity, error recovery, data integrity, rollback |
| **[audit-as-dx-eng](./audit-as-dx-eng)** | Audit through the eyes of a DX Engineer — error messages, CLI ergonomics, defaults, keyboard, config |
| **[audit-as-perf-eng](./audit-as-perf-eng)** | Audit through the eyes of a Performance Engineer — memory, IPC latency, streaming, long-session stability |
| **[audit-as-a11y-eng](./audit-as-a11y-eng)** | Audit for Accessibility — screen readers, keyboard nav, ARIA, focus management, contrast |
| **[full-audit](./full-audit)** | Run all 10 lenses sequentially into one document — plan mode or repo mode |

## Creating a Skill

Each skill is a folder with a `SKILL.md` file:

```
my-skill/
  SKILL.md        # Required — frontmatter + instructions
  references/     # Optional — supporting files
```

The `SKILL.md` needs YAML frontmatter with `name` and `description`:

```yaml
---
name: my-skill
description: What this skill does — this text is used for search and discovery
---

# My Skill

Instructions for the AI agent...
```

## License

MIT — see [LICENSE](./LICENSE).
