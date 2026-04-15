---
title: "GitHub SpecKit"
date: 2026-04-15
draft: false
keywords: ["spec-driven development"]
---

# GitHub SpecKit

## TL;DR

- **Specs before code** — SpecKit enforces a Spec → Plan → Tasks → Code workflow so AI agents have explicit requirements before touching code
- **Works with 30+ agents** — GitHub Copilot, Claude Code, Cursor, Gemini CLI, and others are all supported
- **Constitution file for guardrails** — `.specify/memory/constitution.md` encodes non-negotiable project principles (test coverage, security policies, preferred libraries) inherited by every spec and plan
- **Best for complex, multi-team projects** — skip it for throwaway prototypes and single-file utilities

---

> Toolkit for Spec-Driven Development (SDD) with AI coding agents.

- GitHub: [github/spec-kit](https://github.com/github/spec-kit)
- Docs: [spec-driven.md](https://github.com/github/spec-kit/blob/main/spec-driven.md)
- Official docs site: [github.github.io/spec-kit](https://github.github.io/spec-kit/)

## What It Is

SpecKit enforces **specifications before code**. Instead of prompting an agent and hoping for the best, you define *what* you want, *plan* how to build it, break it into tasks — then let the agent implement. Works with 30 agents: GitHub Copilot, Claude Code, Cursor, Gemini CLI, and others.

## Core Idea

```
Spec → Plan → Tasks → Code
```

Language models are good at pattern completion, bad at mind reading. SpecKit makes requirements explicit so agents have the right context before touching code.

## Installation

```bash
# Via uv (recommended) — pin to a release tag for stability
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git@v0.7.0

# Or via uvx (no install needed)
uvx --from git+https://github.com/github/spec-kit.git@v0.7.0 specify init <PROJECT_NAME>
```

> **Warning:** do not run `uv tool install specify-cli` without the `--from` flag. Unaffiliated packages with the same name exist on PyPI.

Initializing creates two directories:

- `.github/prompts/` — agent-specific prompt files (or `.claude/skills/` when using `--ai-skills`)
- `.specify/` — templates, scripts, memory files (including the constitution)

## Workflow Commands

### Core

| Command | Purpose |
|---|---|
| `/speckit.specify` | Define *what* and *why* — functional requirements, user journeys, success metrics |
| `/speckit.plan` | Define *how* — frameworks, architecture, infra choices |
| `/speckit.tasks` | Break the plan into small, testable units with acceptance criteria |
| `/speckit.implement` | Hand off to the agent for code generation |
| `/speckit.taskstoissues` | Convert task list items into GitHub issues |

### Optional

| Command | Purpose |
|---|---|
| `/speckit.constitution` | Create or update project governing principles |
| `/speckit.clarify` | Clarify underspecified areas (recommended before `/speckit.plan`) |
| `/speckit.analyze` | Cross-artifact consistency and coverage analysis |
| `/speckit.checklist` | Generate custom quality checklists |

## Example Flow

```bash
# 1. Init for a new project with Claude Code as agent
specify init my-api --ai claude-code

# 2. Inside your editor, run:
/speckit.specify
# → produces a spec document: what the system does, user journeys, constraints

/speckit.clarify
# → surfaces ambiguities and underspecified areas before planning

/speckit.plan
# → produces architecture doc: tech stack choices grounded in the constitution

/speckit.tasks
# → produces task list with acceptance criteria per task

/speckit.implement
# → implements task-by-task, each unit reviewable in isolation
```

## constitution.md

A constitution file at `.specify/memory/constitution.md` encodes **non-negotiable principles**: test coverage thresholds, security policies, preferred libraries, compliance rules. Every spec and plan inherits these constraints automatically. Create or update it with `/speckit.constitution`.

```markdown
# constitution.md (example)
- All endpoints must have integration tests (no mocks for DB calls)
- Minimum 80% code coverage enforced in CI
- OWASP Top 10 scanning required before merge
- Prefer Go over Python for backend services
```

## Extensions and Presets

SpecKit ships with 50+ community extensions covering:

- **Integrations** — Jira, Azure DevOps, Confluence, Linear, Trello, GitHub Projects
- **Docs** — auto-generated documentation, architecture diagrams
- **Process** — code review workflows, release orchestration
- **Visibility** — reporting, dashboards

Community **presets** customize templates, commands, and terminology for specific workflows. Manage both via the `specify extension` and `specify preset` CLI subcommands.

## When to Use

**Good fit:**

- Production features with complex or cross-team requirements
- Large codebases where context gets lost
- Compliance-heavy projects needing audit trails
- Multi-developer work needing shared alignment

**Skip it for:**

- Throwaway prototypes
- Single-file utilities
- Solo experiments

## Key Benefit

Specs, plans, and tasks stay in the repo. Future "why did we do this?" questions get answered by documents, not Slack archaeology.

## References

1. [github/spec-kit — GitHub](https://github.com/github/spec-kit) — official repository and source code
2. [Spec-Driven Development with AI — GitHub Blog](https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/) — GitHub's announcement and introduction to SDD
3. [Spec-Driven Development with Spec Kit — Microsoft for Developers](https://developer.microsoft.com/blog/spec-driven-development-spec-kit) — Microsoft's developer guide for SpecKit
4. [GitHub Spec Kit Tutorial 2026 — paperclipped.de](https://www.paperclipped.de/en/blog/github-spec-kit-spec-driven-development/) — hands-on tutorial and walkthrough
5. [SpecKit: Turning Copilot Into a Spec-Driven Teammate — Medium/arconsis](https://medium.com/arconsis/part-5-speckit-turning-github-copilot-into-a-specification-driven-teammate-c84c3cfc2ca4) — practical experience report on using SpecKit with Copilot
6. [SpecKit official docs](https://github.github.io/spec-kit/) — full documentation site
7. [spec-driven.md](https://github.com/github/spec-kit/blob/main/spec-driven.md) — spec-driven development methodology document
