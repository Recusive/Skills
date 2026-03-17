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
