---
title: "Egghead AI-Driven Multi-Agent Framework — Research & Design Plan"
created: 2026-06-20
updated: 2026-06-20
type: plan
tags: [framework, multi-agent, architecture, workflow, planning]
sources: []
status: draft
version: 0.2.0
---

# Egghead AI-Driven Multi-Agent Framework

## Research & Design Plan v0.1.0

**Purpose:** Refactor seluruh sources di `_archive/` dan merancang framework baru untuk AI-Driven Multi-Agent personal software development di atas Hermes Agent runtime.

**Scope:** egghead-wiki project

**Focus:** Domain AI-Driven personal software development dengan 10 agent spesialis

**Methodology Sources:** MattPocock Skills, Superpowers, BMAD-METHOD, Oh-My-OpenAgent (OMO)

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Current State Audit](#2-current-state-audit)
3. [Methodology Synthesis](#3-methodology-synthesis)
4. [Framework Design](#4-framework-design)
5. [Agent Roster Redesign](#5-agent-roster-redesign)
6. [Workflow Lifecycle](#6-workflow-lifecycle)
7. [Skill Architecture](#7-skill-architecture)
8. [Runtime Integration](#8-runtime-integration)
9. [Token Optimization](#9-token-optimization)
10. [Migration Strategy](#10-migration-strategy)
11. [Gap Analysis](#11-gap-analysis)
12. [Research Questions](#12-research-questions)
13. [Next Steps](#13-next-steps)

---

## 1. Executive Summary

### Problem Statement

Egghead-wiki saat ini memiliki 60+ source files di `_archive/` yang merupakan hasil riset dan perancangan framework multi-agent AI untuk personal software development. Namun:

1. **Sources tercampur** — Framework-level docs bercampur dengan project-specific docs (hermes-pingback E2E test, notflix configs)
2. **Framework usang** — Dirancang sebelum Hermes runtime mature, sebelum MattPocock/Superpowers/BMAD mencapai versi stabil
3. **Scope terlalu luas** — Mencoba cover semua use case (personal, project, coding, research, security, DevOps, docs, QA, ops) sekaligus
4. **Tidak terimplementasi** — Framework ada di dokumen tapi belum dijadikan Hermes skills yang bisa di-load dan di-execute

### Objectives

1. **Audit & Refactor** — Analisis semua sources, pisahkan framework-level dari project-specific, identifikasi mana yang masih relevan
2. **Synthesize** — Gabungkan pola terbaik dari MattPocock, Superpowers, BMAD, dan OMO ke dalam satu framework yang kohesif
3. **Design** — Rancang framework baru yang:
   - Terfokus pada AI-Driven personal software development
   - Berjalan di atas Hermes Agent runtime
   - Menggunakan 10 agent spesialis
   - Mengadopsi workflow methods yang terbukti
   - Token-efficient dan practical
4. **Plan** — Buat roadmap implementasi yang bisa di-review dan di-action

### Deliverables

- Dokumen riset ini (plans/01-research-design-plan.md)
- Gap analysis dan research questions
- Framework architecture draft
- Migration strategy
- Next steps yang actionable

---

## 2. Current State Audit

### 2.1 Archive Inventory

`_archive/` berisi 60+ files yang dikategorikan sebagai berikut:

#### Framework-Level Documents (Relevant for Refactoring)

| File | Description | Status | Relevance |
|------|-------------|--------|-----------|
| `KUMACHII-STELLA-Multi-Agent-Workflow-Framework.md` | Framework v1.0 (1750 lines) | Outdated | HIGH — core framework spec |
| `HERMES-BMAD-Multi-Agent-Workflow-Framework.md` | BMAD-adapted framework (1459 lines) | Outdated | HIGH — better structured |
| `AI-Driven-Development-Workflow.md` | 4-framework comparison & integration (782 lines) | Partially outdated | HIGH — synthesis analysis |
| `KUMACHII-STELLA-Multi-Agent-Workflow-Framework-updated.md` | Updated version | Outdated | MEDIUM |
| `kumachii-stella-multi-agent-workflow-framework-v1.1.0-final.md` | v1.1.0 final | Outdated | MEDIUM |
| `kumachii-stella-framework-comparison-merge-notes-v1.1.0.md` | Merge notes | Outdated | LOW |
| `kumachii-stella-framework-v1.1.0-compliance-review.md` | Compliance review | Outdated | LOW |
| `HERMES-BMAD-Multi-Agent-Workflow-Framework-copy.md` | Duplicate | Outdated | NONE |

#### Agent SOUL Files (10 agents)

| File | Agent | Status |
|------|-------|--------|
| `KUMACHII-SOUL.md` | Personal Assistant & Intake | Needs refactoring |
| `STELLA-SOUL.md` | Orchestrator | Needs refactoring |
| `SHAKA-SOUL.md` | Architect | Needs refactoring |
| `EDISON-SOUL.md` | Coding Agent | Needs refactoring |
| `PYTHAGORAS-SOUL.md` | Research Agent | Needs refactoring |
| `ATLAS-SOUL.md` | Security Review | Needs refactoring |
| `YORK-SOUL.md` | QA / Reviewer | Needs refactoring |
| `LILITH-SOUL.md` | DevOps | Needs refactoring |
| `BONNEY-SOUL.md` | Documentation + RAG | Needs refactoring |
| `STUSSY-SOUL.md` | Monitoring + Ops | Needs refactoring |

#### Project-Specific Documents (hermes-pingback E2E test)

| File | Description | Relevance |
|------|-------------|-----------|
| `PRD.md` | PRD for hermes-pingback | LOW — project-specific |
| `ARCHITECTURE.md` | Architecture for hermes-pingback | LOW — project-specific |
| `ADR-001-FRAMEWORK.md` | HTTP framework selection ADR | LOW — project-specific |
| `CODE_REVIEW.md` | Code review for hermes-pingback | LOW |
| `SECURITY_REVIEW.md` | Security review for hermes-pingback | LOW |
| `GATES.md` | Gate decisions for hermes-pingback | LOW |
| `RUNBOOK.md` | Runbook for hermes-pingback | LOW |
| `MANIFEST.md` | RAG manifest for hermes-pingback | LOW |
| `AGENT_DEPLOYMENT_TEMPLATE.md` | Agent deployment template | MEDIUM |
| `E2E_LIFECYCLE_TEST.md` | E2E test spec | LOW |
| `E2E_LIFECYCLE_TEST_REPORT.md` | E2E test report | LOW |

#### Research & Analysis Documents

| File | Description | Relevance |
|------|-------------|-----------|
| `COMPREHENSIVE_RESEARCH_SUMMARY.md` | Research summary | MEDIUM |
| `ai-development-workflow-analysis.md` | Framework comparison | HIGH — analysis |
| `research-opencode-hermes-multiagent.md` | OpenCode + Hermes research | MEDIUM |
| `code-review-graph-vs-rtk-analysis.md` | Token optimization analysis | HIGH |
| `ai-driven-multi-agent-final-plan.md` | Final plan draft | MEDIUM |
| `HERMES_INTERNALS.md` | Hermes runtime reference | HIGH — runtime docs |
| `ENOWX_PROVIDER.md` | enowX provider docs | LOW |
| `HONCHO_MEMORY_INTEGRATION.md` | Honcho memory integration | MEDIUM |
| `RAG_ARCHITECTURE_PLAN.md` | RAG architecture plan | MEDIUM |

#### Migration & Implementation Documents

| File | Description | Relevance |
|------|-------------|-----------|
| `MIGRATION_MAPPING.md` | Migration mapping | MEDIUM |
| `PHASE_5_0_CONCEPTUAL_FIX_PLAN.md` | Phase 5 conceptual fix | LOW |
| `STAGE_A_FOUNDATION_CHECKLIST.md` | Foundation checklist | LOW |
| `STAGE_C_D_E_TASK_PLAN.md` | Task plan | LOW |
| `SPEC_AUDIT.md` | Spec audit | LOW |
| `DEPLOYMENT_SUMMARY.md` | Deployment summary | LOW |
| `V1_1_1_REQUEST_HUMAN_APPROVAL_SPEC.md` | Approval spec | MEDIUM |

#### Skills & Integration Documents

| File | Description | Relevance |
|------|-------------|-----------|
| `hermes-skill-adoption-plan-2026-05-26.md` | Skill adoption plan | MEDIUM |
| `hermes-skill-adoption-rollout-2026-05-26.md` | Skill adoption rollout | MEDIUM |
| `hermes-maf-live-e2e-orchestration-remediation-2026-05-26.md` | Orchestration remediation | LOW |
| `hermes-maf-live-e2e-test-plan-2026-05-26.md` | E2E test plan | LOW |
| `agent-skills-mandate-audit-2026-05-18.md` | Skills mandate audit | MEDIUM |
| `hermes-wiki-research-2026-05-18.md` | Wiki research | MEDIUM |
| `kumachii-stella-skills-architecture-plan-2026-05-21.md` | Skills architecture plan | HIGH |
| `maf-ai-e2e-report-20260526-220239.md` | MAF E2E report | LOW |

#### Summary Documents

| File | Description | Relevance |
|------|-------------|-----------|
| `00-summary.md` | Overall summary | MEDIUM |
| `01-implementation.md` | Implementation summary | MEDIUM |
| `02-configuration.md` | Configuration summary | MEDIUM |
| `03-architecture.md` | Architecture summary | MEDIUM |
| `04-commissioning-testing.md` | Commissioning summary | LOW |
| `README.md` | Project README | MEDIUM |

### 2.2 Current Wiki State

Wiki sudah memiliki:
- **10 entity pages** (agent SOULs) — sudah di-extract dari archive
- **20 concept pages** — sudah di-extract dari archive docs
- **SCHEMA.md** — well-defined domain, taxonomy, conventions
- **index.md** — complete, all 30 pages listed
- **log.md** — 4 entries

### 2.3 Critical Findings

1. **Framework docs sebagian besar usang** — Dirancang untuk konteks "notflix" project dan Hermes runtime versi lama
2. **Agent SOUL files personal** — Menggunakan nama-nama personal (KUMACHII, STELLA, dll) yang terikat dengan konteks spesifik
3. **Tidak ada Hermes profile setup** — Framework mendefinisikan agent tapi belum ada actual Hermes profile configuration
4. **Tidak ada skill implementations** — Workflow phases didefinisikan tapi belum dijadikan Hermes skills
5. **Research docs valuable** — Analisis 4 framework (MattPocock, Superpowers, BMAD, OMO) masih relevan dan menjadi dasar synthesa
6. **Token optimization strategies documented** — RTK, Caveman, CRG analysis ada tapi belum diimplementasikan

---

## 3. Methodology Synthesis

### 3.1 Framework Comparison Matrix

| Dimension | MattPocock Skills | Superpowers | BMAD-METHOD | OMO |
|-----------|------------------|-------------|-------------|-----|
| **Stars** | 138k | 234k | 49.4k | 63k |
| **Version** | v1.0.1 (Jun 2026) | v6.0.3 (Jun 2026) | v6.8.0 (May 2026) | v4.7.5 (Jun 2026) |
| **Type** | Skill instruction files | Agentic skills framework | Full dev methodology | Multi-model orchestrator |
| **Agent Model** | Single generalist | Single generalist | 6 named personas | 9 discipline agents |
| **Process Rigor** | Low (flexible) | High (mandatory) | High (phased, gated) | Medium (hook-driven) |
| **Composability** | High | High | Low-Medium | High |
| **Human-in-Loop** | Strong (grilling) | Strong (TDD, review) | Strong (gates) | Medium (verification) |
| **Token Efficiency** | High (focused context) | High (subagent isolation) | Medium (doc-heavy) | Medium (multi-model) |
| **Hermes Compatible** | Yes (markdown skills) | Yes (markdown skills) | Partial (needs adaptation) | No (OpenCode-specific) |

### 3.2 Patterns to Adopt

#### From MattPocock Skills

| Pattern | Description | Egghead Application |
|---------|-------------|---------------------|
| **Grilling Sessions** | Relentless interview to resolve intent before coding | Phase 0 Discovery — KUMACHII/STELLA interviews user |
| **CONTEXT.md** | Domain glossary maintained by grilling + domain modeling | Project-level glossary for each codebase |
| **Vertical Slice TDD** | One test → one implementation → repeat (not horizontal) | EDISON's implementation workflow |
| **Two-Axis Invocation** | User-invoked (orchestration) vs Model-invoked (auto-discipline) | Skill categorization in Hermes |
| **Composable Skills** | Skills reference other skills via prose (`/skill-name`) | Cross-skill dependencies |
| **Triage State Machine** | Structured issue management with agent-ready briefs | Issue intake and routing |
| **Domain Modeling** | Challenge terms vs glossary, edge-case scenarios | Architecture phase validation |

#### From Superpowers

| Pattern | Description | Egghead Application |
|---------|-------------|---------------------|
| **Mandatory Skill Check** | Agent MUST check for relevant skills before any task | Rosinante SOUL §7 enforcement |
| **Subagent-Driven Development** | Fresh subagent per task, clean context, two-stage review | EDISON task execution isolation |
| **7-Stage Workflow** | Brainstorming → Worktrees → Plans → Subagent → TDD → Review → Finish | Phase lifecycle backbone |
| **Git Worktrees** | Isolated branch per task, clean test baseline | Implementation isolation |
| **Two-Stage Review** | Spec compliance → Code quality | YORK review process |
| **Named Agent Consolidation** | Generic Task dispatch with specific prompts | Agent-as-profile pattern |
| **Verification Before Completion** | Systematic check before claiming done | Quality gate enforcement |

#### From BMAD-METHOD

| Pattern | Description | Egghead Application |
|---------|-------------|---------------------|
| **Phase-Gated Architecture** | 4 phases with quality gates at boundaries | Core workflow structure |
| **Scale-Adaptive** | Quick Flow (small) vs Full Method (complex) | Task complexity routing |
| **Spine Contracts** | Lean source-of-truth docs (ARCHITECTURE-SPINE.md) | Architecture handoff format |
| **Memlog** | Append-only memory as single source of truth | Decision log and state tracking |
| **Implementation Readiness** | PASS/CONCERNS/FAIL check before coding | Gate 2 validation |
| **Agent-as-Persona** | Named agents with 2-letter menu codes | Agent identity system |

##### BMAD Adoption Pitfalls: Adopt Patterns vs Install Directly

| Aspect | Adopt Patterns Only | Install BMAD Directly |
|--------|--------------------|-----------------------|
| **Control** | Full control over implementation | BMAD controls structure, templates, workflows |
| **Complexity** | Low — pick what fits | High — 6 agents, 20+ workflows, TOML config |
| **Overhead** | Minimal — use phases + gates | Significant — _bmad/ directory, config hierarchy, memlog |
| **Hermes Fit** | Good — adapt to Hermes skills | Poor — BMAD assumes Claude Code/Cursor runtime |
| **Maintenance** | Self-maintained | Upstream updates can break customizations |
| **Token Cost** | Low — only load relevant patterns | High — full agent activation lifecycle |
| **Solo Dev** | Ideal — pick Quick Flow or full | Overkill for solo dev unless using Quick Flow |
| **Customization** | Unlimited | Limited by BMAD's TOML merge system |

**Decision:** Adopt BMAD patterns (phases, gates, spine contracts, memlog) as framework backbone. Do NOT install BMAD directly — it's designed for Claude Code/Cursor, not Hermes runtime. Extract the methodology, leave the tooling.

#### From OMO (Oh-My-OpenAgent)

| Pattern | Description | Egghead Application |
|---------|-------------|---------------------|
| **Multi-Model Routing** | Different models for different task types | Hermes model router integration |
| **Category-Based Intent** | Say intent → get right model | Task classification system |
| **Trust-but-Verify** | Orchestrator verifies subagent output | STELLA verification protocol |
| **54+ Lifecycle Hooks** | Comprehensive event system | Hermes skill hooks |
| **Session Continuity** | boulder.json resume on crash | Hermes session persistence |
| **Hash-Anchored Edits** | Verifiable edit operations | Code modification safety |
| **Background Agents** | Parallel task execution | Hermes delegate_task + cron |

### 3.3 Patterns to Avoid

| Pattern | Source | Reason |
|---------|--------|--------|
| Named agent personas (Mary, John, etc.) | BMAD | Overhead for solo dev; Egghead uses role-based agents |
| Full BMAD ceremony for small tasks | BMAD | Use Quick Flow equivalent instead |
| OpenCode-specific hooks | OMO | Not compatible with Hermes runtime |
| Multi-model API key overhead | OMO | Hermes already has model routing via 9Router |
| TypeScript/Bun dependency | OMO, Superpowers | Egghead runs on Python/Hermes |
| Web Bundles (Gemini Gems) | BMAD | Extra tooling; Hermes can handle planning natively |

### 3.4 Synthesis: The Egghead Approach

The Egghead framework combines:

```
MattPocock's alignment techniques (grilling, CONTEXT.md)
    ↓
BMAD's phase-gated structure (4 phases + Quick Flow)
    ↓
Superpowers' mandatory discipline (skill check, TDD, subagent isolation)
    ↓
OMO's orchestration model (multi-model routing, trust-but-verify)
    ↓
Hermes Agent runtime (profiles, skills, delegation, MCP, memory)
```

**Key design principle:** Lightweight by default, rigorous when needed.

- **Small tasks (< 15 min):** Direct execution with skill check. No ceremony.
- **Medium tasks (15 min - 2 hours):** Quick Flow — abbreviated phases, minimal docs.
- **Large tasks (2+ hours):** Full workflow — all phases, gates, documents.

---

## 4. Framework Design

### 4.1 Framework Name

**Egghead AI-Driven Multi-Agent Framework** (Egghead MAF)

### 4.2 Core Objective

Build a multi-agent system for **AI-driven personal software development** that is:

- **Structured** — Phase-gated workflow with clear handoffs
- **Isolated** — Agent profiles with separate context, credentials, memory
- **Auditable** — Decision logs, handoff documents, gate records
- **Token-Efficient** — Smart Zone, focused context, compression strategies
- **Practical** — Works for solo developer on real projects
- **Portable** — Generic role names, reusable across all projects
- **Adaptable** — Scales from quick bugfix to full feature development

### 4.3 Design Principles

#### P1: Alignment Before Execution
Never code from vague input. Every non-trivial task begins with intent clarification.

#### P2: Smart Zone Discipline
Keep context small and focused. Reset between phases. Use separate sessions for review.

#### P3: Documents as Contracts
Handoff between agents happens through documents. Documents are not decoration — they are control points.

#### P4: Skill-First Workflow
Agent MUST check for relevant skills before any task. Skills are mandatory behavioral guides, not suggestions.

#### P5: Vertical Slicing
One story = data + logic + interface + test. Never deliver horizontal slices.

#### P6: Trust-but-Verify
Orchestrator verifies subagent output. Independent review before merge. No single agent can push to production.

#### P7: Human-in-the-Loop
Human approval mandatory for: scope changes, architecture changes, dependency additions, security exceptions, production deployment.

#### P8: Simplicity First
Prefer simple code over clever code. Prefer minimal dependencies. Prefer reversible changes.

### 4.4 Architecture Layers

```
┌──────────────────────────────────────────────────────────────┐
│                    USER / OPERATOR                            │
│            Personal tasks, projects, coding, research         │
└──────────────────────────────┬───────────────────────────────┘
                               │
                               ▼
┌──────────────────────────────────────────────────────────────┐
│ LAYER 1 — PERSONAL INTERFACE                                 │
│ INTAKE                                                       │
│ - User-facing assistant (Telegram primary)                   │
│ - Personal task handler                                      │
│ - Intake and triage                                          │
│ - Grilling sessions (intent alignment)                       │
│ - Forwards structured work to ORCHESTRATOR                   │
└──────────────────────────────┬───────────────────────────────┘
                               │
                               ▼
┌──────────────────────────────────────────────────────────────┐
│ LAYER 2 — ORCHESTRATION                                      │
│ ORCHESTRATOR                                                 │
│ - Workflow state owner                                       │
│ - Phase gate enforcer                                        │
│ - Delegation controller                                      │
│ - Trust-but-verify verifier                                  │
│ - Human approval checkpoint                                  │
└───────────────┬──────────────┬──────────────┬───────────────┘
                │              │              │
                ▼              ▼              ▼
┌────────────────────┐ ┌────────────────────┐ ┌────────────────────┐
│ LAYER 3 — BUILD    │ │ LAYER 4 — CONTROL  │ │ LAYER 5 — OPERATE  │
│ ARCHITECT          │ │ SECURITY           │ │ DEVOPS             │
│ CODER              │ │ QA                 │ │ OPERATOR           │
│ RESEARCHER         │ │ DOCUMENTER         │ │                    │
└────────────────────┘ └────────────────────┘ └────────────────────┘
```

### 4.5 Task Complexity Routing

| Complexity | Duration | Workflow | Agents Involved | Documentation |
|------------|----------|----------|-----------------|---------------|
| **Trivial** | < 5 min | Direct execution | KUMACHII (or any single agent) | None |
| **Small** | 5-30 min | Skill-checked execution | Single agent + skill | Minimal |
| **Medium** | 30 min - 2h | Quick Flow (abbreviated phases) | 2-3 agents | Task brief + summary |
| **Large** | 2h - 8h | Full workflow (7 phases) | All relevant agents | Full document set |
| **Epic** | 8h+ | Full workflow + sprint planning | All agents | Full docs + sprint tracking |

---

## 5. Agent Roster Redesign

### 5.1 Agent Roles (10 Specialists — Generic Role Names)

| # | Agent | Role | Layer | Platform | Access | Can Block |
|---|-------|------|-------|----------|--------|-----------|
| 1 | **Intake** | Personal Assistant & Intake | 1 - Interface | Telegram | Medium | No |
| 2 | **Orchestrator** | Workflow Owner & Gate Enforcer | 2 - Orchestration | Discord + Telegram | High | Yes (gates) |
| 3 | **Architect** | System Design & ADRs | 3 - Build | Discord | High | No |
| 4 | **Coder** | Implementation & TDD | 3 - Build | Discord | High | No |
| 5 | **Researcher** | Research & Analysis | 3 - Build | Discord | Read-only | No |
| 6 | **Security** | Security Review & Threat Modeling | 4 - Control | Discord | High review | Yes |
| 7 | **QA** | Quality Assurance & Review | 4 - Control | Discord | Review + test | Yes |
| 8 | **DevOps** | Deployment & Infrastructure | 5 - Operate | Discord | High (gated) | Yes |
| 9 | **Documenter** | Documentation & RAG | 4 - Control | Discord | Docs + KB write | No |
| 10 | **Operator** | Monitoring & Operations | 5 - Operate | Discord | Ops read (gated) | Yes |

**Note:** Generic role names chosen for portability. Each agent maps to a Hermes profile with isolated SOUL, skills, and credentials.

### 5.2 Agent Responsibility Matrix

| Task Type | Primary | Support | Reviewer | Approval |
|-----------|---------|---------|----------|----------|
| Personal task | Intake | Documenter | User | If sensitive |
| Project planning | Orchestrator | Researcher, Documenter | User | Yes |
| Architecture | Architect | Researcher, Security | Orchestrator | Yes |
| Coding | Coder | Architect, Documenter | QA | For merge |
| Research | Researcher | Documenter | Orchestrator | If decision-impacting |
| Security review | Security | Architect, DevOps | Orchestrator | Yes for exceptions |
| QA/testing | QA | Coder, Security | Orchestrator | Yes for release |
| Deployment | DevOps | Security, Operator | Orchestrator | Yes for production |
| Documentation | Documenter | All agents | QA | If source-of-truth changes |
| Monitoring/Ops | Operator | DevOps, Security | Orchestrator | For remediation |

### 5.3 Agent SOUL Refactoring Plan

Each SOUL file needs:

1. **Remove project-specific references** — Remove "notflix", specific tokens, specific chat IDs
2. **Add skill integration** — Reference which skills the agent uses
3. **Add Hermes runtime mapping** — How the agent maps to Hermes profiles
4. **Add workflow phase ownership** — Which phases the agent owns
5. **Standardize format** — Consistent frontmatter and structure

**Template for refactored SOUL:**

```yaml
---
title: [Agent Name]
created: YYYY-MM-DD
updated: YYYY-MM-DD
type: entity
tags: [agent, multi-agent, hermes, [role-specific-tags]]
sources: []
confidence: high
agent_role: [role]
layer: [1-5]
platform: [telegram/discord/both]
access_level: [medium/high/read-only/high-review]
can_block: [true/false]
workflow_phases: [list of owned phases]
skills_used: [list of skills this agent loads]
---
```

---

## 6. Workflow Lifecycle

### 6.1 Seven-Phase Workflow

```
Phase 0: DISCOVERY    → Owner: Intake/Orchestrator → Gate 0
Phase 1: PLANNING     → Owner: Orchestrator        → Gate 1
Phase 2: SOLUTIONING  → Owner: Architect           → Gate 2
Phase 3: IMPLEMENTATION → Owner: Coder             → Gate 3
Phase 4: REVIEW       → Owner: QA                  → Gate 4
Phase 5: DEPLOY       → Owner: DevOps              → Gate 5
Phase 6: OPERATE      → Owner: Operator            → Gate 6
```

### 6.2 Phase Details

#### Phase 0: Discovery & Alignment

**Goal:** Understand the problem, align intent, build shared vocabulary.

**Owner:** Intake (intake) + Orchestrator (orchestration)

**Activities:**
1. Grilling session — interrogate user about what they want (MattPocock pattern)
2. Build shared language — CONTEXT.md glossary (MattPocock pattern)
3. Domain research — Researcher background research
4. Product brief — structured task brief

**Outputs:**
- `docs/intake/task-brief-{id}.md`
- `CONTEXT.md` (if new project)

**Gate 0 Checklist:**
- [ ] Problem is clear
- [ ] Goal is clear
- [ ] Scope is bounded
- [ ] Out-of-scope is listed
- [ ] Assumptions are listed
- [ ] Success criteria are defined

**Decision:** PASS / CONCERNS / FAIL

#### Phase 1: Planning

**Goal:** Define what to build, break work into stories.

**Owner:** Orchestrator

**Support:** Researcher, Documenter

**Activities:**
1. Create PRD from brief (BMAD pattern)
2. Define user stories with acceptance criteria
3. Define non-functional requirements
4. Create CONTEXT.md if not exists
5. Break into epics and stories

**Outputs:**
- `docs/PRD.md`
- `docs/epics/*.md`
- Updated `CONTEXT.md`

**Gate 1 Checklist:**
- [ ] PRD exists
- [ ] Stories exist with acceptance criteria
- [ ] NFRs exist
- [ ] Success metrics are measurable
- [ ] Scope is stable

**Decision:** PASS / CONCERNS / FAIL

#### Phase 2: Solutioning

**Goal:** Define how to build it, break work into implementable units.

**Owner:** Architect

**Support:** Orchestrator, Researcher, Security

**Activities:**
1. Create architecture document (BMAD spine pattern)
2. Define components and boundaries
3. Define data flow and API contracts
4. Create ADRs for major decisions
5. Run implementation readiness check (BMAD pattern)
6. Create epics and stories from architecture

**Outputs:**
- `docs/architecture.md` (or `ARCHITECTURE-SPINE.md`)
- `docs/ADRs/*.md`
- `docs/readiness-report.md`

**Gate 2 Checklist:**
- [ ] Architecture exists
- [ ] ADRs exist for major decisions
- [ ] Components are clear
- [ ] Boundaries are clear
- [ ] Security model is defined
- [ ] Deployment model is defined
- [ ] Implementation readiness is PASS

**Decision:** PASS / CONCERNS / FAIL

#### Phase 3: Implementation

**Goal:** Build story by story, with discipline.

**Owner:** Coder

**Support:** Architect (architecture clarification), Researcher (research), Security (security-sensitive code)

**Activities (per story):**
1. Pick next story from sprint backlog
2. Git worktree for isolation (Superpowers pattern)
3. Write tests FIRST — RED → GREEN → REFACTOR (TDD)
4. Implement minimum code to pass
5. Verify everything (Karpathy pattern)
6. Refactor if needed (no premature abstraction)
7. Create atomic commit
8. Self-review against plan
9. Mark story done, pick next

**Outputs:**
- Source code
- Tests
- Implementation summary
- Risk notes

**Gate 3 Checklist:**
- [ ] All stories done
- [ ] All tests pass
- [ ] No architecture violations
- [ ] No hardcoded secrets
- [ ] No unapproved dependencies

**Decision:** PASS / CONCERNS / FAIL

#### Phase 4: Review

**Goal:** Independent validation and quality assurance.

**Owner:** QA

**Support:** Security (security), Documenter (docs)

**Activities:**
1. Code review (two-stage: spec compliance → code quality)
2. Security review (ATLAS)
3. Test coverage validation
4. Architecture compliance check
5. Documentation review (BONNEY)
6. Fix review issues

**Outputs:**
- `docs/reviews/code-review-{id}.md`
- `docs/security/security-review-{id}.md`
- Updated code (fixes)

**Gate 4 Checklist:**
- [ ] Code review passed
- [ ] Security review passed
- [ ] All tests still pass
- [ ] Documentation updated
- [ ] No blocking issues

**Decision:** PASS / CONCERNS / FAIL

#### Phase 5: Deploy

**Goal:** Deploy with approval, verify, rollback plan ready.

**Owner:** DevOps

**Support:** Security (security), Operator (monitoring)

**Activities:**
1. Prepare deployment plan
2. Validate environment
3. Deploy to staging
4. Run smoke tests
5. Prepare rollback plan
6. Request human approval for production
7. Deploy to production (if approved)
8. Verify health

**Outputs:**
- `docs/deployment.md`
- `docs/runbook.md`
- `docs/commissioning-report.md`

**Gate 5 Checklist:**
- [ ] Staging deployment successful
- [ ] Smoke tests pass
- [ ] Rollback plan exists
- [ ] Human approval received
- [ ] Production deployment verified

**Decision:** PASS / CONCERNS / FAIL

#### Phase 6: Operate

**Goal:** Monitor, maintain, and learn.

**Owner:** Operator

**Support:** DevOps, Security

**Activities:**
1. Monitor service health
2. Track metrics and logs
3. Detect and respond to incidents
4. Update runbook with lessons learned
5. Feed learnings back to framework

**Outputs:**
- `docs/ops/health-report.md`
- `docs/ops/incident-report.md`
- Updated runbook

### 6.3 Quick Flow (for Small Tasks)

For tasks that don't warrant full 7-phase workflow:

```
Quick Flow: Intent → Plan → Implement → Review → Done
```

**When to use:**
- Bug fixes with clear scope
- Simple features (< 15 stories)
- Refactoring with clear boundaries
- Documentation updates

**Abbreviated phases:**
1. **Intent:** Grilling session (5 min) → task brief
2. **Plan:** Quick architecture note + story list
3. **Implement:** TDD with worktree isolation
4. **Review:** Self-review + optional peer review
5. **Done:** Commit, clean up, summary

---

## 7. Skill Architecture

### 7.1 Skill Categories

Based on the methodology synthesis, Egghead needs these skill categories:

#### Workflow Skills (User-Invoked)

| Skill | Purpose | Phase | Based On |
|-------|---------|-------|----------|
| `/grill` | Intent alignment interview | 0 | MattPocock grilling |
| `/plan` | Create PRD from brief | 1 | BMAD bmad-prd |
| `/architect` | Design architecture + ADRs | 2 | BMAD bmad-architecture |
| `/implement` | TDD implementation workflow | 3 | Superpowers TDD |
| `/review` | Code review workflow | 4 | Superpowers code review |
| `/deploy` | Deployment workflow | 5 | BMAD deployment |
| `/quick-flow` | Abbreviated workflow for small tasks | All | BMAD Quick Flow |

#### Discipline Skills (Model-Invoked)

| Skill | Purpose | Agent | Based On |
|-------|---------|-------|----------|
| `tdd` | Red-green-refactor discipline | Coder | MattPocock TDD |
| `ponytail` | Ladder of laziness before coding | Coder | Ponytail v4.7.0 |
| `diagnosing-bugs` | Systematic debugging | Coder | MattPocock diagnosing-bugs |
| `domain-modeling` | Glossary + edge cases | Architect | MattPocock domain-modeling |
| `codebase-design` | Module design + interfaces | Architect | MattPocock codebase-design |
| `systematic-debugging` | 4-phase root cause | Coder | Superpowers systematic-debugging |
| `verification-before-completion` | Pre-claim checks | All | Superpowers verification |
| `curator` | Resolve skill conflicts, prioritize relevant skills | Orchestrator | Custom — conflict resolution |

#### Agent Skills (Agent-Specific)

| Skill | Purpose | Agent |
|-------|---------|-------|
| `handoff` | Structured agent-to-agent transfer | All |
| `security-review` | Threat modeling + risk assessment | Security |
| `qa-review` | Quality assurance checklist | QA |
| `deployment-checklist` | Pre-deploy validation | DevOps |
| `monitoring-setup` | Observability configuration | Operator |
| `docs-generation` | Documentation from code | Documenter |

#### Optimization Skills (Model-Invoked)

| Skill | Purpose | Based On |
|-------|---------|----------|
| `rtk-compression` | Command output compression | Hermes RTK |
| `caveman-output` | Response compression | Hermes Caveman |
| `code-review-graph` | Blast radius analysis | Hermes CRG |
| `smart-zone` | Context optimization | Hermes Smart Zone |

### 7.2 Skill Loading Protocol

Following MattPocock's two-axis model:

```
User-invoked skills: /grill, /plan, /architect, /implement, /review, /deploy
Model-invoked skills: tdd, diagnosing-bugs, domain-modeling, codebase-design
```

**Loading rule:** Agent MUST check for relevant skills before any task (Superpowers mandatory check pattern).

**Conflict resolution:** Curator skill runs first to resolve any conflicts between loaded skills. Curator prioritizes skills relevant to current task and phase.

### 7.3 Skill Implementation Format

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

---

## 8. Runtime Integration

### 8.1 Hermes Profile Mapping

Each agent maps to a Hermes profile with isolated SOUL, skills, and credentials:

| Agent | Hermes Profile | Model | Isolation |
|-------|---------------|-------|-----------|
| Intake | `intake` | TBD | Profile-based |
| Orchestrator | `orchestrator` | TBD | Profile-based |
| Architect | `architect` | TBD | Profile-based |
| Coder | `coder` | TBD | Profile-based |
| Researcher | `researcher` | TBD | Read-only |
| Security | `security` | TBD | Profile-based |
| QA | `qa` | TBD | Profile-based |
| DevOps | `devops` | TBD | Gated |
| Documenter | `documenter` | TBD | Profile-based |
| Operator | `operator` | TBD | Profile-based |

**Status:** Model assignment per profile belum ditentukan. Mapping model akan ditentukan setelah framework terimplementasi dan diuji. Gunakan Hermes default model untuk semua profile sementara.

### 8.2 Communication Channels

| Channel | Purpose | Participants |
|---------|---------|--------------|
| Telegram (primary) | User-facing approvals, personal tasks | Intake ↔ User |
| Discord (inter-agent) | Agent-to-agent handoffs, status reports | All agents |
| Discord (approvals) | Gate approval prompts from Orchestrator | Orchestrator → User |

**Handoff Protocol:** Primary handoff mechanism via structured skill & gates principle. Discord used for ad-hoc communication with format yang ditentukan & disesuaikan.

### 8.3 Hermes Tool Integration

| Hermes Tool | Egghead Usage |
|-------------|---------------|
| `delegate_task` | Subagent dispatch for parallel work (per-agent delegation study needed — different functions = different setups) |
| `skill_view` | Load workflow and discipline skills |
| `session_search` | Recall past sessions and decisions |
| `hindsight_*` | Long-term memory for agent learning |
| `cronjob` | Background monitoring (case-dependent: some cron, some always-on) |
| `mcp_sequential_thinking` | Complex reasoning for architecture/planning |
| `todo` | Sprint tracking and story management |
| `execute_code` | Batch operations and analysis |

### 8.4 Knowledge Architecture

**Memory Sharing:** Dual approach — Hindsight external memory provider untuk persistent cross-profile memory + Knowledge base untuk structured knowledge sharing.

```
~/.hermes/knowledge/
├── global/
│   └── USER_PROFILE.md          # User preferences, role
├── topics/
│   ├── egghead-framework.md     # Framework overview
│   ├── agent-roster.md          # Agent roles and responsibilities
│   └── workflow-phases.md       # Phase lifecycle reference
└── repos/
    └── <project-name>/
        ├── CONTEXT.md           # Domain glossary
        ├── architecture.md      # Project architecture
        └── conventions.md       # Project conventions

~/.hermes/handoffs/              # Cross-agent handoff documents
~/egghead-wiki/                  # LLM-Wiki knowledge base
~/.hindsight/                    # Hindsight external memory
```

---

## 9. Token Optimization

### 9.1 Strategies

| Strategy | Description | Source | Implementation |
|----------|-------------|--------|----------------|
| **RTK** | Compress terminal output to essential info | Hermes | Skill: `rtk-compression` |
| **Caveman** | Compress responses to minimal viable output | Hermes | Skill: `caveman-output` |
| **Code Review Graph** | Analyze blast radius before editing | Hermes | Skill: `code-review-graph` |
| **Smart Zone** | Keep context focused, reset between phases | Hermes SOUL | Built into workflow |
| **Ponytail** | Ladder of laziness: YAGNI -> stdlib -> native -> dependency -> one-liner -> minimum | Ponytail v4.7.0 | EDISON coding discipline |
| **Subagent Isolation** | Fresh context per task, no pollution | Superpowers | `delegate_task` |
| **Vertical Slicing** | Small, focused tasks instead of large monolithic ones | MattPocock | Story-based workflow |
| **Focused Skill Loading** | Load only relevant skills per phase | MattPocock | Two-axis invocation |
| **Spine Contracts** | Lean source-of-truth docs, not verbose specs | BMAD | ARCHITECTURE-SPINE.md |

### 9.2 Cost Optimization

**Status:** Model assignment per phase/gate belum ditentukan. Saat ini fokus pada optimasi token usage melalui strategies di atas (RTK, Caveman, CRG, Ponytail, Smart Zone).

**Future:** Setelah framework terimplementasi dan diuji, tentukan model optimal per phase berdasarkan empiris. Evaluasi berdasarkan:
- Task complexity actual vs model capability
- Token cost per phase
- Quality of output per model
- Latency requirements

---

## 10. Migration Strategy

### 10.1 Phase 1: Archive Cleanup (Week 1)

**Goal:** Separate framework-level from project-specific docs.

**Actions:**
1. Create `_archive/framework-level/` and `_archive/project-specific/` directories
2. Move framework docs to `framework-level/`
3. Move hermes-pingback-specific docs to `project-specific/`
4. Update wiki pages to reference correct sources
5. Run lint to verify no broken links

### 10.2 Phase 2: SOUL Refactoring (Week 1-2)

**Goal:** Refactor all 10 agent SOUL files with generic role names.

**Actions:**
1. Rename agents to generic role names (Intake, Orchestrator, Architect, Coder, Researcher, Security, QA, DevOps, Documenter, Operator)
2. Remove project-specific references (notflix, tokens, chat IDs)
3. Add skill integration references (including Ponytail for Coder)
4. Add Hermes runtime mapping (profile, model TBD)
5. Add workflow phase ownership
6. Standardize format across all SOULs
7. Update wiki entity pages to match new names

### 10.3 Phase 3: Framework Documentation (Week 2-3)

**Goal:** Write definitive framework documentation.

**Actions:**
1. Write `EGGHEAD-FRAMEWORK.md` — master framework spec
2. Write `WORKFLOW-LIFECYCLE.md` — detailed phase docs
3. Write `AGENT-OPERATING-GUIDE.md` — how agents work together
4. Write `SKILL-ARCHITECTURE.md` — skill categories and loading
5. Update SCHEMA.md with new tags and conventions
6. Create all missing concept pages (the 10 broken wikilink targets)

### 10.4 Phase 4: Skill Implementation (Week 3-4)

**Goal:** Implement core skills as Hermes skills.

**Actions:**
1. Install MattPocock skills and adapt for Hermes
2. Install Superpowers skills and adapt for Hermes
3. Implement `ponytail` skill for Coder (ladder of laziness)
4. Implement `curator` skill for conflict resolution
5. Implement `/grill` skill (intent alignment)
6. Implement `/plan` skill (PRD creation)
7. Implement `tdd` skill (TDD discipline)
8. Implement `handoff` skill (agent transfer)
9. Test skill loading and execution

### 10.5 Phase 5: Runtime Setup (Week 4-5)

**Goal:** Configure Hermes profiles for agents.

**Actions:**
1. Create Hermes profiles for each agent
2. Configure model routing per agent
3. Set up communication channels
4. Test agent delegation workflow
5. Verify isolation and security

### 10.6 Phase 6: Integration Testing (Week 5-6)

**Goal:** End-to-end testing of the framework.

**Actions:**
1. Run Quick Flow on a real small task
2. Run full workflow on a real medium task
3. Verify all gates work
4. Verify all handoffs work
5. Collect metrics and feedback
6. Iterate on framework based on findings

---

## 11. Gap Analysis

### 11.1 Critical Gaps (Must Fix)

| Gap | Impact | Effort | Priority |
|-----|--------|--------|----------|
| No Hermes profile setup for agents | Framework unimplementable | Medium | P0 |
| No skill implementations | Workflow phases are theoretical | High | P0 |
| Agent SOULs tied to specific context | Not portable or reusable | Medium | P0 |
| Framework docs reference outdated runtime | Misleading | Low | P0 |

### 11.2 Important Gaps (Should Fix)

| Gap | Impact | Effort | Priority |
|-----|--------|--------|----------|
| No CONTEXT.md convention defined | Domain modeling incomplete | Low | P1 |
| No sprint tracking integration | Story management unclear | Medium | P1 |
| No cost estimation model | Token budget unknown | Medium | P1 |
| No monitoring/observability spec | Ops phase incomplete | Medium | P1 |
| Missing 10 concept pages (broken wikilinks) | Wiki incomplete | Low | P1 |

### 11.3 Nice-to-Have Gaps

| Gap | Impact | Effort | Priority |
|-----|--------|--------|----------|
| No CI/CD pipeline for framework changes | Manual testing only | Low | P2 |
| No benchmark suite for agent performance | Can't measure improvement | High | P2 |
| No multi-project support design | Solo-dev only | Medium | P2 |
| No enterprise/team workflow adaptation | Solo-dev only | High | P2 |

---

## 12. Research Questions

### 12.1 Open Questions — ANSWERED (Gilang Review 2026-06-20)

1. **Agent Names:** ✅ **Generic role names** — Intake, Orchestrator, Architect, Coder, Researcher, Security, QA, DevOps, Documenter, Operator. Lebih portabel.

2. **Domain Scope:** ✅ **Full personal + project software development** — Cover personal tasks (office, campus, finance) DAN project software development.

3. **Platform Choice:** ✅ **Discord (inter-agent) + Telegram (user-facing)** — Tetap.

4. **Model Budget:** ✅ **No hard limit, optimize aggressively** — Tidak ada batasan token/cost, tapi optimalisasi token harus tetap dilakukan seoptimal mungkin.

5. **Project Priority:** ✅ **Baseline framework untuk semua project Gilang** — Reusable, bukan project-specific.

6. **Skill Source:** ✅ **Install MattPocock + adapt for Hermes** — `npx skills@latest add mattpocock/skills`, lalu adaptasi untuk Hermes runtime.

7. **BMAD Integration:** ✅ **Adopt patterns only, not install** — Adopsi phase gates lifecycle sebagai pattern. Buat pitfalls comparison adopt vs install. Jangan install BMAD langsung.

8. **Superpowers Adoption:** ✅ **Install as skill library + adapt for Hermes** — Install Superpowers, adapt skills untuk Hermes runtime.

### 12.2 Technical Research Questions — ANSWERED (Gilang Review 2026-06-20)

1. **Hermes Multi-Profile:** ✅ Each profile has isolated SOUL & skills. Knowledge base & memory sharing via **Hindsight external memory provider**. Setup perlu dikaji lebih lanjut.

2. **Delegation Depth:** ✅ **Per-agent study needed** — Fungsional setiap agent berbeda, jadi delegation setup pasti berbeda. Tidak ada satu ukuran untuk semua.

3. **Cross-Profile Communication:** ✅ **Handoffs via skill & gates principle** sebagai prioritas utama. Komunikasi lain via Discord dengan format yang ditentukan & disesuaikan.

4. **Skill Conflicts:** ✅ **Curator skill** — Skill pertama yang menangani konflik. Curator memprioritaskan skills yang relevan untuk digunakan.

5. **Memory Sharing:** ✅ **Dual approach** — (1) External memory provider Hindsight untuk persistent cross-profile memory, (2) Knowledge base (Handoffs / LLM-Wiki / RAG) untuk structured knowledge sharing.

6. **Cron for Monitoring:** ✅ **Case-dependent** — Beberapa case sebagai cron job monitoring, beberapa always-on. Tentukan per case saat implementasi.

### 12.3 New Research Items

1. **Ponytail Integration:** Research cara install Ponytail v4.7.0 untuk Hermes runtime. Ponytail adalah "ladder of laziness" skill yang memaksa agent berpikir sebelum nulis kode. Benchmark: -54% LOC, -22% tokens, -20% cost. Repository: https://github.com/DietrichGebert/ponytail

2. **Curator Skill Design:** Rancang Curator skill yang bisa resolve skill conflicts. Pattern: load all relevant skills → detect conflicts → apply priority rules → output final skill set.

3. **Hindsight External Memory Setup:** Research cara setup Hindsight sebagai external memory provider yang bisa diakses oleh semua profiles.

4. **MattPocock Skill Adaptation:** Research adaptation path dari MattPocock skills (Claude Code format) ke Hermes SKILL.md format.

5. **Superpowers Skill Adaptation:** Research adaptation path dari Superpowers skills ke Hermes SKILL.md format.

---

## 13. Next Steps

### Immediate (This Session)

- [ ] Review this plan document with Gilang
- [ ] Answer research questions (Section 12.1)
- [ ] Decide on agent naming convention
- [ ] Decide on framework scope (personal dev only vs full personal + project)

### Short-Term (This Week)

- [ ] Phase 1: Archive cleanup — separate framework-level from project-specific
- [ ] Phase 2: Begin SOUL refactoring for first 3 agents (KUMACHII, STELLA, SHAKA)
- [ ] Research Hermes multi-profile setup capabilities

### Medium-Term (Next 2 Weeks)

- [ ] Phase 3: Write definitive framework documentation
- [ ] Phase 4: Implement first 3 skills (`/grill`, `/plan`, `tdd`)
- [ ] Create missing wiki pages for broken wikilinks

### Long-Term (Next Month)

- [ ] Phase 5: Runtime setup — configure Hermes profiles
- [ ] Phase 6: Integration testing on real project
- [ ] Iterate based on findings

---

## Appendix A: Source Attribution

| Pattern | Source Framework | Egghead Application |
|---------|-----------------|---------------------|
| Grilling sessions | MattPocock Skills | Phase 0 Discovery |
| CONTEXT.md glossary | MattPocock Skills | Domain modeling |
| Vertical Slice TDD | MattPocock Skills | EDISON workflow |
| Two-axis invocation | MattPocock Skills | Skill categorization |
| Mandatory skill check | Superpowers | Rosinante SOUL §7 |
| Subagent isolation | Superpowers | delegate_task usage |
| 7-stage workflow | Superpowers | Phase lifecycle |
| Git worktrees | Superpowers | Implementation isolation |
| Two-stage review | Superpowers | YORK review process |
| Phase-gated architecture | BMAD-METHOD | Core workflow structure |
| Scale-adaptive routing | BMAD-METHOD | Task complexity routing |
| Spine contracts | BMAD-METHOD | Architecture handoff |
| Memlog | BMAD-METHOD | Decision log |
| Implementation readiness | BMAD-METHOD | Gate 2 validation |
| Multi-model routing | OMO | Hermes model router |
| Category-based intent | OMO | Task classification |
| Trust-but-verify | OMO | STELLA verification |
| Session continuity | OMO | Hermes session persistence |

## Appendix B: Glossary

| Term | Definition |
|------|------------|
| **Grilling** | Relentless interview to resolve intent before coding (MattPocock) |
| **CONTEXT.md** | Domain glossary maintained by grilling sessions |
| **Smart Zone** | Keeping context small and focused for better agent performance |
| **Dumb Zone** | When context is too large and agent performance degrades |
| **Vertical Slice** | One story = data + logic + interface + test (not horizontal layers) |
| **Spine Contract** | Lean source-of-truth document that other docs project from (BMAD) |
| **Memlog** | Append-only memory log as single source of truth (BMAD) |
| **Quick Flow** | Abbreviated workflow for small tasks (BMAD) |
| Ponytail | Ponytail | Ladder of laziness before coding | EDISON/Coder discipline |
| Trust-but-Verify | OMO | Orchestrator verifies subagent output |
| **Subagent Isolation** | Fresh context per task to prevent pollution (Superpowers) |
| Curator | Egghead Custom | Resolve skill conflicts, prioritize relevant skills |
| **Ponytail** | Ladder of laziness: YAGNI -> stdlib -> native -> dependency -> one-liner -> minimum (DietrichGebert/ponytail) |
| **Curator** | Skill that resolves conflicts between loaded skills, prioritizing relevance |
| **Two-Stage Review** | Spec compliance check → Code quality check (Superpowers) |
| **Gate Decision** | PASS (proceed), CONCERNS (proceed with monitoring), FAIL (block) |

---

**Document Status:** REVIEWED — Gilang feedback applied (2026-06-20). Pending: implementation.

**Next Review:** After Phase 1 (archive cleanup) completion

**Version History:**
- v0.1.0 (2026-06-20) — Initial research and design plan
- v0.2.0 (2026-06-20) — Gilang review applied: generic role names, Ponytail addition, BMAD pitfalls, all research questions answered
