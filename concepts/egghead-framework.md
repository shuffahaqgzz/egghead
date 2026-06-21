---
title: "Egghead AI-Driven Multi-Agent Framework"
created: 2026-06-20
updated: 2026-06-20
type: concept
tags: [framework, multi-agent, architecture, orchestration, workflow]
sources: []
confidence: high
---

# Egghead AI-Driven Multi-Agent Framework

**Version:** 1.0.0-draft
**Status:** Research & Design
**Runtime:** Hermes Agent (Nous Research)

## Purpose

Baseline framework untuk AI-driven personal + project software development. Reusable across semua project Gilang. Menggabungkan pola terbaik dari MattPocock Skills, Superpowers, BMAD-METHOD, dan Oh-My-OpenAgent (OMO) ke dalam satu framework yang berjalan di atas Hermes Agent runtime.

## Design Principles

| # | Principle | Description |
|---|-----------|-------------|
| P1 | **Alignment Before Execution** | Never code from vague input. Every non-trivial task begins with intent clarification. |
| P2 | **Smart Zone Discipline** | Keep context small and focused. Reset between phases. Use separate sessions for review. |
| P3 | **Documents as Contracts** | Handoff between agents happens through documents. Documents are control points, not decoration. |
| P4 | **Skill-First Workflow** | Agent MUST check for relevant skills before any task. Skills are mandatory, not suggestions. |
| P5 | **Vertical Slicing** | One story = data + logic + interface + test. Never deliver horizontal slices. |
| P6 | **Trust-but-Verify** | Orchestrator verifies subagent output. Independent review before merge. |
| P7 | **Human-in-the-Loop** | Human approval mandatory for scope changes, architecture changes, dependency additions, security exceptions, production deployment. |
| P8 | **Simplicity First** | Prefer simple code over clever code. Prefer minimal dependencies. Prefer reversible changes. |

## Architecture Layers

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

## Agent Roster (10 Specialists)

| # | Agent | Role | Layer | Platform | Access | Can Block |
|---|-------|------|-------|----------|--------|-----------|
| 1 | **Intake** | Personal Assistant & Intake | 1 - Interface | Telegram | Medium | No |
| 2 | **Orchestrator** | Workflow Owner & Gate Enforcer | 2 - Orchestration | Discord + Telegram | High | Yes |
| 3 | **Architect** | System Design & ADRs | 3 - Build | Discord | High | No |
| 4 | **Coder** | Implementation & TDD | 3 - Build | Discord | High | No |
| 5 | **Researcher** | Research & Analysis | 3 - Build | Discord | Read-only | No |
| 6 | **Security** | Security Review & Threat Modeling | 4 - Control | Discord | High review | Yes |
| 7 | **QA** | Quality Assurance & Review | 4 - Control | Discord | Review + test | Yes |
| 8 | **DevOps** | Deployment & Infrastructure | 5 - Operate | Discord | High (gated) | Yes |
| 9 | **Documenter** | Documentation & RAG | 4 - Control | Discord | Docs + KB write | No |
| 10 | **Operator** | Monitoring & Operations | 5 - Operate | Discord | Ops read (gated) | Yes |

## Task Complexity Routing

| Complexity | Duration | Workflow | Agents | Documentation |
|------------|----------|----------|--------|---------------|
| **Trivial** | < 5 min | Direct execution | Single agent | None |
| **Small** | 5-30 min | Skill-checked execution | Single + skill | Minimal |
| **Medium** | 30 min - 2h | Quick Flow | 2-3 agents | Task brief + summary |
| **Large** | 2h - 8h | Full workflow (7 phases) | Relevant agents | Full document set |
| **Epic** | 8h+ | Full workflow + sprint | All agents | Full docs + tracking |

## Workflow Lifecycle

```
Phase 0: DISCOVERY    → Owner: Intake/Orchestrator → Gate 0
Phase 1: PLANNING     → Owner: Orchestrator        → Gate 1
Phase 2: SOLUTIONING  → Owner: Architect           → Gate 2
Phase 3: IMPLEMENTATION → Owner: Coder             → Gate 3
Phase 4: REVIEW       → Owner: QA                  → Gate 4
Phase 5: DEPLOY       → Owner: DevOps              → Gate 5
Phase 6: OPERATE      → Owner: Operator            → Gate 6
```

See [[workflow-lifecycle]] for detailed phase documentation.

## Gate Decisions

| Decision | Meaning |
|----------|---------|
| **PASS** | Proceed to next phase |
| **CONCERNS** | Proceed with monitoring and risk acknowledgment |
| **FAIL** | Block — remediate before continuing |

## Skill Architecture

See [[skill-architecture]] for complete skill taxonomy and loading protocol.

**Core principle:** Agent MUST check for relevant skills before any task (Superpowers mandatory check pattern).

## Runtime Integration

See [[runtime-integration]] for Hermes profile mapping, tool integration, and knowledge architecture.

## Token Optimization

| Strategy | Description |
|----------|-------------|
| **RTK** | Command output compression |
| **Caveman** | Response compression |
| **Code Review Graph** | Blast radius analysis |
| **Smart Zone** | Context optimization |
| **Ponytail** | Ladder of laziness before coding |
| **Subagent Isolation** | Fresh context per task |

## Communication

| Channel | Purpose |
|---------|---------|
| Telegram | User-facing approvals, personal tasks |
| Discord | Inter-agent handoffs, status reports |

**Primary handoff mechanism:** Structured skill & gates principle. Discord for ad-hoc communication.

## Memory & Knowledge

- **Hindsight:** External memory provider for persistent cross-profile memory
- **LLM-Wiki:** Interlinked markdown knowledge base
- **Handoffs:** Cross-agent handoff documents
- **Knowledge Base:** Per-project CONTEXT.md, architecture, conventions

## Source Authority Hierarchy

When conflicts arise between sources, this hierarchy determines precedence:

| Rank | Source Type | Example |
|------|------------|---------|
| 1 | Source-of-truth documents | Official specs, blessed designs |
| 2 | Architecture + ADRs | Architecture docs, ADRs |
| 3 | PRD + acceptance criteria | Product requirements, test cases |
| 4 | Code + tests | Implementation artifacts |
| 5 | Deployment + runbook docs | Runbooks, deployment configs |
| 6 | Research reports | Researcher outputs |
| 7 | Generated summaries | Documenter RAG summaries |
| 8 | Chat history | Discord/Telegram conversations |

**Key rule:** Generated summaries must not override primary sources.

## Source Attribution

| Pattern | Source | Application |
|---------|--------|-------------|
| Grilling sessions | MattPocock | Phase 0 Discovery |
| CONTEXT.md | MattPocock | Domain modeling |
| Vertical Slice TDD | MattPocock | Coder workflow |
| Two-axis invocation | MattPocock | Skill categorization |
| Mandatory skill check | Superpowers | Agent discipline |
| Subagent isolation | Superpowers | delegate_task usage |
| 7-stage workflow | Superpowers | Phase lifecycle |
| Phase-gated architecture | BMAD | Core workflow |
| Scale-adaptive routing | BMAD | Task complexity |
| Spine contracts | BMAD | Architecture handoff |
| Multi-model routing | OMO | Hermes model router |
| Trust-but-verify | OMO | Orchestrator verification |
| Ponytail | Ponytail | Coding token optimization |
