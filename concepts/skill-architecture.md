---
title: "Skill Architecture"
created: 2026-06-20
updated: 2026-06-22
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

## Implemented Skills (Phase 4 Complete — 24/24)

### Workflow Skills (User-Invoked) — 6 skills

| Skill | Purpose | Phase | Status |
|-------|---------|-------|--------|
| `grill` | Intent alignment interview | 0 | ✅ Implemented |
| `plan` | Create PRD / implementation plan | 1 | ✅ Built-in |
| `brainstorming` | Structured problem exploration | 0/2 | ✅ Implemented |
| `architect` | Design architecture + ADRs | 2 | ✅ Implemented |
| `implement` | TDD implementation workflow | 3 | ✅ Implemented |
| `review` | Two-stage code review | 4 | ✅ Implemented |
| `deploy` | Deployment workflow | 5 | ✅ Implemented |
| `quick-flow` | Abbreviated workflow for small tasks | All | ✅ Implemented |

### Discipline Skills (Model-Invoked) — 8 skills

| Skill | Purpose | Agent | Status |
|-------|---------|-------|--------|
| `tdd` | Red-green-refactor discipline | Coder | ✅ Implemented |
| `ponytail` | Ladder of laziness before coding | Coder | ✅ Implemented |
| `diagnosing-bugs` | Structured bug diagnosis | Coder | ✅ Implemented |
| `debugging` | Systematic root cause analysis | Coder | ✅ Implemented |
| `domain-modeling` | Glossary + edge cases | Architect | ✅ Implemented |
| `codebase-design` | Module design + interfaces | Architect | ✅ Implemented |
| `verification-before-completion` | Pre-claim checks | All | ✅ Implemented |
| `curator` | Resolve skill conflicts | Orchestrator | ✅ Implemented |

### Agent Skills (Agent-Specific) — 6 skills

| Skill | Purpose | Agent | Status |
|-------|---------|-------|--------|
| `handoffs` | Structured agent-to-agent transfer | All | ✅ Built-in |
| `security-review` | Threat modeling + risk assessment | Security | ✅ Implemented |
| `qa-review` | Quality assurance checklist | QA | ✅ Implemented |
| `deployment-checklist` | Pre-deploy validation | DevOps | ✅ Implemented |
| `monitoring-setup` | Observability configuration | Operator | ✅ Implemented |
| `docs-generation` | Documentation from code | Documenter | ✅ Implemented |

### Optimization Skills (Model-Invoked) — 4 skills

| Skill | Purpose | Status |
|-------|---------|--------|
| `rtk-compression` | Command output compression | ✅ Implemented |
| `caveman-output` | Response compression | ✅ Implemented |
| `code-review-graph` | Blast radius analysis | ✅ Implemented |
| `smart-zone` | Context optimization | ✅ Implemented |

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
metadata:
  hermes:
    tags: [category, tags]
    related_skills: [other-skills]
---
```

## Conflict Resolution Matrix

| Conflict | Resolution |
|----------|-----------|
| TDD vs Ponytail on test scope | Ponytail wins for trivial (skip test), TDD wins for non-trivial |
| Grill vs Plan on process | Grill wins if ambiguous, Plan wins if clear |
| TDD vs Debugging on approach | TDD for new code, Debugging for existing bugs |
| Ponytail vs everything on complexity | Ponytail is advisory — suggests simpler, doesn't block |
| Brainstorming vs Plan on exploration | Brainstorming for decisions, Plan for implementation |

## Methodology Gap Skills (§3.2 Patterns)

Additional skills covering patterns from §3.2 methodology synthesis that
weren't in §7.1 but were identified as adopted patterns.

### Pattern-Based Skills (5 skills)

| Skill | Source Pattern | Framework | Purpose |
|-------|---------------|-----------|---------|
| `triage` | Triage State Machine | MattPocock | Issue intake, categorize, prioritize, route, brief |
| `worktrees` | Git Worktrees | Superpowers | Isolated branch per task, clean test baseline |
| `spine-contract` | Spine Contracts | BMAD | Lean source-of-truth architecture doc |
| `memlog` | Memlog | BMAD | Append-only decision/memory log |
| `hash-anchored-edits` | Hash-Anchored Edits | OMO | Verifiable edit operations via checksum |

### Coverage Matrix: §3.2 Patterns

| Framework | Pattern | Skill | Status |
|-----------|---------|-------|--------|
| MattPocock | Grilling Sessions | `grill` | ✅ |
| MattPocock | CONTEXT.md | `domain-modeling` | ✅ |
| MattPocock | Vertical Slice TDD | `tdd` | ✅ |
| MattPocock | Two-Axis Invocation | Architecture design | ✅ |
| MattPocock | Composable Skills | Cross-skill refs | ✅ |
| MattPocock | Triage State Machine | `triage` | ✅ |
| MattPocock | Domain Modeling | `domain-modeling` | ✅ |
| Superpowers | Mandatory Skill Check | SOUL + `curator` | ✅ |
| Superpowers | Subagent-Driven Dev | `implement` | ✅ |
| Superpowers | 7-Stage Workflow | Workflow skills | ✅ |
| Superpowers | Git Worktrees | `worktrees` | ✅ |
| Superpowers | Two-Stage Review | `review` | ✅ |
| Superpowers | Named Agent Consolidation | Agent-as-profile | ✅ |
| Superpowers | Verification Before Completion | `verification-before-completion` | ✅ |
| BMAD | Phase-Gated Architecture | Workflow lifecycle | ✅ |
| BMAD | Scale-Adaptive | `quick-flow` | ✅ |
| BMAD | Spine Contracts | `spine-contract` | ✅ |
| BMAD | Memlog | `memlog` | ✅ |
| BMAD | Implementation Readiness | `verification-before-completion` | ✅ |
| BMAD | Agent-as-Persona | Agent-as-profile | ✅ |
| OMO | Multi-Model Routing | Hermes runtime | ✅ |
| OMO | Category-Based Intent | Task complexity routing | ✅ |
| OMO | Trust-but-Verify | `verification` + `curator` | ✅ |
| OMO | 54+ Lifecycle Hooks | Hermes plugin system | ✅ |
| OMO | Session Continuity | Hermes session persistence | ✅ |
| OMO | Hash-Anchored Edits | `hash-anchored-edits` | ✅ |
| OMO | Background Agents | Hermes delegate + cron | ✅ |

**§3.2 Coverage: 27/27 patterns implemented (100%)**

## Source Attribution

| Skill | Source | Adaptation |
|-------|--------|-----------|
| `grill` | MattPocock `grill-me` | Full rewrite (stub was 7 lines) |
| `tdd` | MattPocock `tdd` | Python-first, Egghead workflow |
| `ponytail` | DietrichGebert `ponytail` | Egghead integration notes |
| `brainstorming` | Egghead-native | Superpowers blocked by security |
| `debugging` | Egghead-native | Superpowers blocked by security |
| `curator` | Egghead-original | Novel conflict resolution |
| All others | Egghead-original | Written from design plan §7.1 |
