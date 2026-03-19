---
name: full-audit
description: Run all 10 expert audit lenses sequentially, writing everything into ONE comprehensive document. Works in TWO modes — (1) Plan mode: provide a plan file path to audit a plan before implementation. (2) Repo mode: audit actual code changes (uncommitted changes, branch/PR vs main, or general repo health). Use this skill when the user says "full audit", "run all audits", "audit everything", "complete audit", "full spectrum audit", "10-lens audit", "audit the repo", "audit my changes", "audit this PR", or wants every expert perspective examined. This is the nuclear option — every lens, every perspective, one document.
---

# Full Spectrum Audit

You are running a **full spectrum audit** — all 10 expert lenses, sequentially, writing everything into **ONE document**: `reviews/audit-plan-full.md`.

**Do NOT write separate files per lens.** Everything goes into one document under headings.

This is thorough by design. It takes time. That's the point.

---

## Detect Mode

Check what the user provided:

- **Plan path given** (e.g., `docs/plans/tracked/todo/some-plan.md`) → **Plan Mode**
- **No path given** OR user said "audit repo" / "audit changes" / "audit this PR" → **Repo Mode**

---

## Repo Mode: Determine Scope

If no plan path was provided, ask the user what to audit:

```
What would you like me to audit?

1. **Uncommitted changes** — audit your current working tree changes (staged + unstaged)
2. **Branch vs main** — audit all commits on the current branch since it diverged from main (PR review)
3. **General repo health** — broad codebase health check against conventions and best practices

Which one? (1/2/3)
```

Based on their answer, gather the audit material:

### Option 1: Uncommitted Changes
```bash
git diff                    # unstaged changes
git diff --staged           # staged changes
git status                  # overview of what's changed
```
Read every changed file in full.

### Option 2: Branch vs Main
```bash
git log main..HEAD --oneline              # commits on this branch
git diff main...HEAD                       # all changes vs main
git diff main...HEAD --stat               # files changed summary
```
Read every changed file in full.

### Option 3: General Repo Health
No specific diff — each lens reads the codebase broadly. Focus on recent files, core files, and convention compliance.

---

## Phase 1: Read & Scope

**Plan mode:** Read the plan document completely.
**Repo mode:** Read all changed files and the diff.

Then determine which of the 10 lenses are **relevant**.

**Relevance rules:**

| Lens | Skip when... |
|------|-------------|
| Design Engineer | Backend-only, no UI changes |
| UX Engineer | No user-facing behavior changes |
| Frontend Engineer | No React/TS/CSS changes |
| Backend Engineer | No Rust/Tauri changes |
| Structural Engineer | Single-file change, no new modules |
| Repo Maintainer | Never skip |
| Production Readiness | Never skip |
| DX Engineer | No user-facing behavior changes |
| Performance Engineer | No new state, listeners, IPC, or I/O |
| Accessibility Engineer | No interactive UI changes |

After reading, announce:
- Which lenses will run (and why)
- Which lenses are being skipped (and why)
- Ask the user: "This will run N lenses. Ready to proceed?"

If the user confirms, proceed. If they want to adjust, honor that.

## Phase 2: Sequential Audit

Run each relevant lens **one at a time**, in this order (user-facing surface → infrastructure):

1. Design Engineer
2. UX Engineer
3. Frontend Engineer
4. Backend Engineer
5. Structural Engineer
6. Repo Maintainer
7. Production Readiness
8. DX Engineer
9. Performance Engineer
10. Accessibility Engineer

For each lens:

### Step: Announce
```
--- Lens N/10: [Expert Name] ---
```

### Step: Invoke the skill
Use the Skill tool:
- `Skill("audit-as-design-eng")`
- `Skill("audit-as-ux-eng")`
- `Skill("audit-as-frontend-eng")`
- `Skill("audit-as-backend-eng")`
- `Skill("audit-as-structural-eng")`
- `Skill("audit-as-repo-maintainer")`
- `Skill("audit-as-prod-readiness")`
- `Skill("audit-as-dx-eng")`
- `Skill("audit-as-perf-eng")`
- `Skill("audit-as-a11y-eng")`

Follow the skill's analysis instructions completely — read files, check gotchas, evaluate through the lens.

**IMPORTANT: Do NOT let the lens write a separate report file.** Instead, collect all findings from each lens and hold them for Phase 3. If a lens writes to a file, that's fine as intermediate work, but the FINAL output is ONE combined document.

### Step: Record findings
After each lens completes, capture:
- Lens name and verdict
- Critical issues (full detail)
- Recommendations (full detail)
- Nice-to-haves
- The signature section output (Visual State Coverage, User Journey Map, etc.)

### Step: Continue to next lens

---

## Phase 3: Write the Single Document

After ALL lenses have completed, write **ONE file**: `reviews/audit-plan-full.md`

This document contains EVERYTHING — the master summary at the top, then the FULL output of each lens under its own heading. Not summaries — the complete lens output.

### Document Structure

```markdown
# Full Spectrum Audit: [Title]

**Date**: [current date]
**Mode**: [Plan / Uncommitted Changes / Branch vs Main / Repo Health]
**Source**: [plan path OR branch name OR "working tree"]
**Lenses Run**: [N]/10
**Lenses Skipped**: [list with reasons]

---

## Master Verdict: [APPROVE / APPROVE WITH CHANGES / NEEDS REWORK]

[2-3 sentence justification synthesizing across ALL lenses.]

## Verdict by Lens

| # | Lens | Verdict | Critical | Recommendations |
|---|------|---------|----------|-----------------|

## All Critical Issues (Must Fix)

Collected from every lens, deduplicated, ordered by severity:

| # | Issue | Lens(es) | File(s) | Impact | Fix |
|---|-------|----------|---------|--------|-----|

## Cross-Cutting Concerns

Issues flagged by 2+ lenses — systemic problems:

| Concern | Flagged By | Root Cause | Unified Fix |
|---------|-----------|------------|-------------|

## All Recommendations

| # | Recommendation | Lens | File(s) | Fix |
|---|---------------|------|---------|-----|

---

# Lens Reports

---

## 1. Design Engineer

**Verdict**: [APPROVE / APPROVE WITH CHANGES / NEEDS REWORK]

[FULL lens output here — everything the design engineer found, including:
- Files reviewed table
- Critical issues table
- Recommendations table
- Design System Compliance section
- Visual State Coverage matrix
- Verdict Details (Token Fidelity, State Coverage, Dark Mode, Animation, Consistency)]

---

## 2. UX Engineer

**Verdict**: [APPROVE / APPROVE WITH CHANGES / NEEDS REWORK]

[FULL lens output — User Journey Map, issues, recommendations, Interaction Consistency Check, Verdict Details]

---

## 3. Frontend Engineer

**Verdict**: [APPROVE / APPROVE WITH CHANGES / NEEDS REWORK]

[FULL lens output — Render Path Analysis, Type Safety Assessment, issues, recommendations, Verdict Details]

---

## 4. Backend Engineer

**Verdict**: [APPROVE / APPROVE WITH CHANGES / NEEDS REWORK]

[FULL lens output — Error Path Analysis, Resource Lifecycle Audit table, issues, recommendations, Verdict Details]

---

## 5. Structural Engineer

**Verdict**: [APPROVE / APPROVE WITH CHANGES / NEEDS REWORK]

[FULL lens output — Dependency Map, Boundary Crossing Analysis, issues, recommendations, Verdict Details]

---

## 6. Repo Maintainer

**Verdict**: [APPROVE / APPROVE WITH CHANGES / NEEDS REWORK]

[FULL lens output — Convention Compliance Checklist, Reuse Opportunities, issues, recommendations, Verdict Details]

---

## 7. Production Readiness

**Verdict**: [APPROVE / APPROVE WITH CHANGES / NEEDS REWORK]

[FULL lens output — Failure Mode Analysis table, Environment Checklist, issues, recommendations, Verdict Details]

---

## 8. DX Engineer

**Verdict**: [APPROVE / APPROVE WITH CHANGES / NEEDS REWORK]

[FULL lens output — Error Message Audit table, Developer Journey, issues, recommendations, Verdict Details]

---

## 9. Performance Engineer

**Verdict**: [APPROVE / APPROVE WITH CHANGES / NEEDS REWORK]

[FULL lens output — Resource Budget table, Hot Path Analysis, issues, recommendations, Verdict Details]

---

## 10. Accessibility Engineer

**Verdict**: [APPROVE / APPROVE WITH CHANGES / NEEDS REWORK]

[FULL lens output — Keyboard Navigation Walkthrough, ARIA Checklist, issues, recommendations, Verdict Details]
```

**For skipped lenses:** Include a brief entry:
```markdown
## N. [Lens Name]

**Skipped** — [reason, e.g., "No Rust/Tauri changes in this plan"]
```

---

## Phase 4: Output

After writing the document:

1. Print: `Full spectrum audit written to reviews/audit-plan-full.md`
2. Print the **Master Verdict**
3. Print the **Verdict by Lens** table
4. Print the **All Critical Issues** table
5. Print the **Cross-Cutting Concerns** table

## Policies

- **ONE file.** Everything goes into `reviews/audit-plan-full.md`. No separate lens files.
- **Full lens output, not summaries.** Each lens section contains the COMPLETE analysis — tables, checklists, verdict details, everything.
- **Every lens runs independently.** Don't let findings from Lens 1 bias Lens 5.
- **Deduplication in the master section.** Critical issues and recommendations that appear in multiple lenses get listed once in the master tables with all source lenses noted. Each individual lens section still contains its own full findings.
- **Cross-cutting concerns are the gold.** If 2+ lenses flag related issues, elevate it.
- **The master verdict is not a vote.** One critical NEEDS REWORK can override nine APPROVEs.
- **Repo mode is code review, not plan review.** Findings reference specific lines, functions, and patterns in actual files.
