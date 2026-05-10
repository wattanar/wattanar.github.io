---
title: How to Use Agent Skills in OpenCode
tags:
  - OpenCode
  - AI
  - Developer Tools
---

OpenCode includes a powerful **Agent Skills** system — a collection of engineering workflow templates that guide how AI agents approach different development tasks. Instead of guessing what to do next, skills encode the processes senior engineers follow.

<!--more-->

## What Are Agent Skills?

Agent Skills are specialized workflows organized by development phase. Each skill tells the agent exactly how to approach a specific type of task — from refining vague ideas to shipping to production.

The key insight: **skills prevent common mistakes by enforcing structured processes**. A bug fix follows a different workflow than a new feature, and skills make sure the agent uses the right one.

## How Skills Work

When you give OpenCode a task, it identifies the development phase and applies the corresponding skill:

```
Task arrives
    │
    ├── Vague idea? ──────────────→ idea-refine
    ├── New feature/change? ───────→ spec-driven-development
    ├── Have a spec, need tasks? ──→ planning-and-task-breakdown
    ├── Implementing code? ────────→ incremental-implementation
    │   ├── UI work? ─────────────→ frontend-ui-engineering
    │   └── API work? ────────────→ api-and-interface-design
    ├── Writing/running tests? ────→ test-driven-development
    ├── Something broke? ──────────→ debugging-and-error-recovery
    ├── Reviewing code? ───────────→ code-review-and-quality
    ├── Committing/branching? ─────→ git-workflow-and-versioning
    ├── CI/CD work? ───────────────→ ci-cd-and-automation
    └── Deploying/launching? ──────→ shipping-and-launch
```

## Configuring Skills

Skills are stored in `~/.config/opencode/skills/` as `SKILL.md` files. Each skill contains:

1. **When to use it** — triggers and conditions
2. **The workflow** — step-by-step process the agent follows
3. **Verification steps** — how to confirm the work is correct
4. **Common failure modes** — what to avoid

### Skill Discovery

The agent automatically discovers and applies skills. You don't need to manually select one — just describe your task and the agent picks the right workflow.

For a complete feature, the typical sequence is:

```
1. idea-refine                 → Refine vague ideas
2. spec-driven-development     → Define what we're building
3. planning-and-task-breakdown → Break into verifiable chunks
4. incremental-implementation  → Build slice by slice
5. test-driven-development     → Prove each slice works
6. code-review-and-quality     → Review before merge
7. git-workflow-and-versioning → Clean commit history
8. documentation-and-adrs      → Document decisions
9. shipping-and-launch         → Deploy safely
```

## Example: Building a Feature

Here's how a real workflow looks when you ask OpenCode to build something:

### 1. Start with a Spec

If you say "add dark mode to my app," the agent loads `spec-driven-development` and writes a spec before coding:

```markdown
# Spec: Dark Mode

## Objective
Add dark mode support to the application...

## Commands
Build: npm run build
Dev: npm run dev

## Success Criteria
- Toggle persists in localStorage
- No flash of light theme on page load
- All components pass WCAG contrast ratios
```

### 2. Break Into Tasks

Next, `planning-and-task-breakdown` creates discrete tasks:

```
- [ ] Task: Add theme context provider
  - Acceptance: Theme state available globally
  - Verify: React DevTools shows ThemeProvider

- [ ] Task: Create dark mode CSS variables
  - Acceptance: All colors have dark variants
  - Verify: Build succeeds, no lint errors
```

### 3. Implement Incrementally

Each task is built one at a time with `test-driven-development` — test first, then code, then verify.

## Core Operating Behaviors

All skills share these non-negotiable rules:

- **Surface assumptions** — state what you're assuming before implementing
- **Manage confusion** — stop and ask when requirements conflict
- **Push back** — point out problems with proposed approaches
- **Enforce simplicity** — prefer the boring, obvious solution
- **Maintain scope** — touch only what you're asked to touch
- **Verify, don't assume** — "seems right" is never enough

## Tips for Best Results

1. **Be specific in your requests** — "fix the login button" is better than "fix login"
2. **Let the agent write specs** — for anything non-trivial, a spec prevents rework
3. **Answer clarification questions** — when the agent surfaces assumptions, respond quickly
4. **Review before approving** — the agent will ask for review at each phase gate

## Available Skills

| Skill | One-Line Summary |
|-------|-----------------|
| idea-refine | Refine ideas through structured thinking |
| spec-driven-development | Requirements before code |
| planning-and-task-breakdown | Decompose into small tasks |
| incremental-implementation | Build thin vertical slices |
| source-driven-development | Verify against official docs |
| context-engineering | Right context at the right time |
| frontend-ui-engineering | Production-quality UI |
| api-and-interface-design | Stable interface contracts |
| test-driven-development | Failing test first |
| browser-testing-with-devtools | Chrome DevTools verification |
| debugging-and-error-recovery | Reproduce → localize → fix → guard |
| code-review-and-quality | Five-axis review |
| security-and-hardening | OWASP prevention |
| performance-optimization | Measure first, optimize what matters |
| git-workflow-and-versioning | Atomic commits, clean history |
| ci-cd-and-automation | Automated quality gates |
| documentation-and-adrs | Document the why |
| shipping-and-launch | Pre-launch checklist, rollback plan |

## Summary

Agent Skills turn OpenCode from a code generator into a disciplined engineering partner. The skills encode processes that prevent the most common AI failures — wrong assumptions, scope creep, and skipping verification.

Next time you start a feature, let the agent write a spec first. You'll be surprised how much clearer the implementation becomes.
