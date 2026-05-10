---
title: How to Use Agent Skills in OpenCode
tags:
  - OpenCode
  - AI
  - Developer Tools
---

AI coding assistants are getting better at writing code, but they still struggle with one thing: **process**. They jump straight into implementation without understanding the problem, skip verification, and often overcomplicate simple tasks.

OpenCode solves this with **Agent Skills** — a system that encodes engineering workflows so the agent follows the same structured process a senior developer would.

<!--more-->

## The Problem With AI Coding Assistants

If you've used any AI coding tool, you've probably seen these patterns:

- You say "add a search feature" and it builds a full Elasticsearch integration when a simple filter would do
- It makes assumptions about your architecture and runs with them
- It writes 500 lines of code when 50 would suffice
- It skips tests because "it looks right"

These aren't bugs in the AI — they're missing processes. Senior engineers don't make these mistakes because they follow workflows: clarify requirements, break down tasks, implement incrementally, verify at each step.

Agent Skills encode those workflows.

## What Are Agent Skills?

Agent Skills are specialized workflow templates organized by development phase. Each skill tells the agent exactly how to approach a specific type of task.

Here's how the agent decides which skill to use:

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

The agent picks the right skill automatically based on your task description.

## How Skills Work in Practice

Let's walk through a real example: adding dark mode to a web application.

### Step 1: Write a Spec First

When you say "add dark mode to my app," the agent doesn't start writing CSS. It loads `spec-driven-development` and writes a spec:

```markdown
# Spec: Dark Mode

## Objective
Add dark mode support with a toggle that persists across sessions.

## Success Criteria
- Toggle persists in localStorage
- No flash of light theme on page load
- All components pass WCAG AA contrast ratios
- Respects prefers-color-scheme system setting

## Open Questions
- Should dark mode be the default, or follow system preference?
```

This forces clarity before any code is written. The spec surfaces assumptions that would otherwise cause rework later.

### Step 2: Break Into Tasks

Once the spec is approved, `planning-and-task-breakdown` creates discrete tasks:

```
- [ ] Task: Add theme context provider
  - Acceptance: Theme state available globally
  - Verify: DevTools shows ThemeProvider with correct initial value

- [ ] Task: Create dark mode CSS variables
  - Acceptance: All colors have light and dark variants
  - Verify: Build succeeds, no lint errors

- [ ] Task: Build toggle component
  - Acceptance: Clicking toggle switches theme and saves to localStorage
  - Verify: Manual test + unit test for localStorage interaction
```

Each task is small enough to complete in one focused session, with explicit acceptance criteria and a verification step.

### Step 3: Implement Incrementally

Each task follows `test-driven-development` — write the failing test first, then make it pass, then verify. No task moves forward until its verification step passes.

## Core Operating Behaviors

All skills share six rules that prevent common AI failures:

| Rule | What It Prevents |
|------|-----------------|
| **Surface assumptions** | Wrong assumptions about architecture or requirements |
| **Manage confusion** | Plowing ahead when requirements conflict |
| **Push back** | Implementing bad approaches without questioning them |
| **Enforce simplicity** | Overcomplicated solutions when simple ones exist |
| **Maintain scope** | Unsolicited refactoring of unrelated code |
| **Verify, don't assume** | "Looks right" code that breaks at runtime |

## Available Skills

Here's the full list of built-in skills and when to use them:

| Skill | When to Use |
|-------|-------------|
| idea-refine | You have a vague idea and need to clarify it |
| spec-driven-development | Starting any non-trivial feature or change |
| planning-and-task-breakdown | You have a spec and need implementation tasks |
| incremental-implementation | Building features slice by slice |
| source-driven-development | Working with frameworks where correctness matters |
| context-engineering | The agent needs better project context |
| frontend-ui-engineering | Building or modifying user interfaces |
| api-and-interface-design | Designing APIs or module boundaries |
| test-driven-development | Writing tests, fixing bugs, changing behavior |
| browser-testing-with-devtools | Verifying anything that runs in a browser |
| debugging-and-error-recovery | Something broke and you need the root cause |
| code-review-and-quality | Reviewing code before merging |
| security-and-hardening | Handling user input, auth, or external integrations |
| performance-optimization | Performance regressions or slow load times |
| git-workflow-and-versioning | Committing, branching, or resolving conflicts |
| ci-cd-and-automation | Setting up or modifying build/deploy pipelines |
| documentation-and-adrs | Recording architectural decisions |
| shipping-and-launch | Preparing to deploy to production |

## Tips for Best Results

1. **Be specific in your requests** — "fix the login button that doesn't submit" works better than "fix login"
2. **Let the agent write specs** — for anything that takes more than 30 minutes, a spec prevents hours of rework
3. **Answer clarification questions** — when the agent surfaces assumptions, respond quickly. Silent assumptions are the #1 cause of wrong implementations
4. **Review at each phase gate** — the agent asks for review between spec → plan → tasks → implementation. Use those checkpoints
5. **Don't skip verification** — if a task says "run tests and they pass," actually run the tests

## Where Skills Live

Skills are stored as `SKILL.md` files in `~/.config/opencode/skills/`. Each file contains:

- When to use the skill (triggers)
- The step-by-step workflow
- Verification checkpoints
- Common failure modes to avoid

You can also create custom skills for your team's specific workflows — just add a `SKILL.md` file to that directory.

## Summary

The difference between a good AI coding session and a frustrating one usually comes down to **process**, not capability. Agent Skills give the agent the structure that experienced developers already follow naturally.

The biggest takeaway: **always start with a spec for non-trivial changes**. Fifteen minutes writing a spec saves hours of debugging misunderstood requirements.
