---
title: "Skill Architecture"
created: 2026-06-20
updated: 2026-06-20
type: concept
tags: [skills, architecture, loading, workflow, discipline]
sources: []
confidence: high
---

# Skill Architecture

Skill taxonomy dan loading protocol untuk [[egghead-framework]].

## Design Principles

1. **Two-axis invocation** (MattPocock): User-invoked (orchestration) vs Model-invoked (auto-discipline)
2. **Mandatory check** (Superpowers): Agent MUST check for relevant skills before any task
3. **Composable** (MattPocock): Skills reference other skills via prose (`/skill-name`)
4. **Conflict resolution**: Curator skill resolves conflicts between loaded skills

## Skill Categories

### Workflow Skills (User-Invoked)

| Skill | Purpose | Phase | Based On |
|-------|---------|-------|----------|
| `/grill` | Intent alignment interview | 0 | MattPocock grilling |
| `/plan` | Create PRD from brief | 1 | BMAD bmad-prd |
| `/architect` | Design architecture + ADRs | 2 | BMAD bmad-architecture |
| `/implement` | TDD implementation workflow | 3 | Superpowers TDD |
| `/review` | Code review workflow | 4 | Superpowers code review |
| `/deploy` | Deployment workflow | 5 | BMAD deployment |
| `/quick-flow` | Abbreviated workflow for small tasks | All | BMAD Quick Flow |

### Discipline Skills (Model-Invoked)

| Skill | Purpose | Agent | Based On |
|-------|---------|-------|----------|
| `tdd` | Red-green-refactor discipline | Coder | MattPocock TDD |
| `ponytail` | Ladder of laziness before coding | Coder | Ponytail v4.7.0 |
| `diagnosing-bugs` | Systematic debugging | Coder | MattPocock |
| `domain-modeling` | Glossary + edge cases | Architect | MattPocock |
| `codebase-design` | Module design + interfaces | Architect | MattPocock |
| `systematic-debugging` | 4-phase root cause | Coder | Superpowers |
| `verification-before-completion` | Pre-claim checks | All | Superpowers |
| `curator` | Resolve skill conflicts, prioritize | Orchestrator | Custom |

### Agent Skills (Agent-Specific)

| Skill | Purpose | Agent |
|-------|---------|-------|
| `handoff` | Structured agent-to-agent transfer | All |
| `security-review` | Threat modeling + risk assessment | Security |
| `qa-review` | Quality assurance checklist | QA |
| `deployment-checklist` | Pre-deploy validation | DevOps |
| `monitoring-setup` | Observability configuration | Operator |
| `docs-generation` | Documentation from code | Documenter |

### Optimization Skills (Model-Invoked)

| Skill | Purpose | Based On |
|-------|---------|----------|
| `rtk-compression` | Command output compression | Hermes RTK |
| `caveman-output` | Response compression | Hermes Caveman |
| `code-review-graph` | Blast radius analysis | Hermes CRG |
| `smart-zone` | Context optimization | Hermes Smart Zone |

## Loading Protocol

### Step 1: Identify Task Type
Determine if task is trivial, small, medium, large, or epic.

### Step 2: Check for Relevant Skills
Agent MUST scan available skills before any task. Use `skills_list` to discover.

### Step 3: Load Skills
Load relevant skills via `skill_view(name)`. Load order:
1. Workflow skill (if applicable)
2. Discipline skills (model-invoked)
3. Agent-specific skills
4. Optimization skills (if token-heavy task)

### Step 4: Resolve Conflicts
If multiple skills have conflicting instructions:
1. Curator skill runs first
2. Curator prioritizes skills relevant to current task and phase
3. Curator outputs final skill set

### Step 5: Execute with Discipline
Follow loaded skills strictly. Do not skip steps.

## Skill Format

Each skill follows Hermes SKILL.md format:

```yaml
---
name: skill-name
description: "One-line description of what this skill does and when to use it."
version: 1.0.0
author: Egghead
license: MIT
platforms: [linux]
metadata:
  hermes:
    tags: [category, tags]
    category: [workflow|discipline|agent|optimization]
    related_skills: [other-skills]
---
```

## Source Skills

### MattPocock Skills (Install + Adapt)

Install: `npx skills@latest add mattpocock/skills`

Key skills to adapt:
- `grill-me` → `/grill` workflow skill
- `tdd` → `tdd` discipline skill
- `diagnosing-bugs` → `diagnosing-bugs` discipline skill
- `domain-modeling` → `domain-modeling` discipline skill
- `codebase-design` → `codebase-design` discipline skill

### Superpowers (Install + Adapt)

Key skills to adapt:
- `test-driven-development` → `tdd` discipline skill
- `systematic-debugging` → `systematic-debugging` discipline skill
- `requesting-code-review` → `/review` workflow skill
- `verification-before-completion` → `verification-before-completion` discipline skill

### Ponytail (Install + Adapt)

Install from: `github.com/DietrichGebert/ponytail`

Key skill:
- `ponytail` → `ponytail` discipline skill (ladder of laziness)
