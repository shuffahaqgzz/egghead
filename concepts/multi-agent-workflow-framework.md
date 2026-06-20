---
title: Multi-Agent Workflow Framework (KUMACHII-STELLA v1.1.0)
created: 2026-06-20
updated: 2026-06-20
type: concept
tags:
  - multi-agent
  - workflow
  - framework
  - kumachii
  - stella
  - hermes
sources:
  - HERMES-BMAD-Multi-Agent-Workflow-Framework.md
  - KUMACHII-STELLA-Multi-Agent-Workflow-Framework.md
  - KUMACHII-STELLA-Multi-Agent-Workflow-Framework-updated.md
  - kumachii-stella-multi-agent-workflow-framework-v1.1.0-final.md
  - kumachii-stella-framework-comparison-merge-notes-v1.1.0.md
  - kumachii-stella-framework-v1.1.0-compliance-review.md
---

# Multi-Agent Workflow Framework (KUMACHII-STELLA v1.1.0)

The KUMACHII-STELLA Multi-Agent Workflow Framework is a deployment-ready operating documentation for a personal, project, software, research, security, QA, DevOps, documentation, RAG, monitoring, and operations multi-agent workflow. It evolved through multiple versions from the original HERMES-BMAD framework to the final merged v1.1.0.

## Core Objective

Build a multi-agent system that is:
- structured
- role-specific
- auditable
- token-efficient
- secure by design
- isolated by runtime profile
- controlled by quality gates
- adaptable for personal and project workflows

Core separation:
```text
KUMACHII = personal assistant and user-facing intake
STELLA   = orchestrator, workflow state owner, and project controller
Specialist agents = execution, validation, security, documentation, deployment, and operations
```

## Framework Foundation (6 Layers)

1. **SOP Discipline** — alignment first, Smart Zone, vertical slicing, TDD, human review
2. **BMAD Structure** — discovery, planning, solutioning, implementation, review, deploy, operate
3. **OMO-Style Orchestration** — orchestrator, specialist agents, read-only consultation, background research
4. **Hermes Runtime Isolation** — dedicated profiles, isolated credentials, isolated memory, isolated sessions
5. **Token Optimization** — context reset, focused scope, RTK, Caveman, code-review-graph, LSP, AST-grep, RAG retrieval
6. **Operational Control** — monitoring, runbooks, rollback, incident response, periodic review

## Design Principles

### Alignment Before Execution
Every major task must define: intent, scope, out-of-scope, constraints, assumptions, expected output, success criteria, owner agent, reviewer agent.

### Smart Zone Discipline
Agents perform better with focused context. Split large tasks into small tasks, use vertical slices, reset context between major phases, use separate sessions for review, do not load the whole codebase unless required.

### Documents as Contracts
Documents are control points, not decoration. Required documents include CONTEXT.md, product brief, PRD, architecture, ADRs, epics/stories, risks, reviews, security, deployment, runbook, operations, and RAG docs.

### Human-in-the-Loop
Human approval is mandatory for: scope changes, architecture changes, dependency additions, security exceptions, credential changes, production deployment, rollback decision, destructive operations, final acceptance.

## Agent Roster (10 Agents)

| Agent | Role | Purpose | Access Level | Runtime |
|---|---|---|---|---|
| KUMACHII | Personal Assistant | Office work, content, education, finance, personal productivity, intake | Medium | Light or dedicated |
| STELLA | Orchestrator | Project management, workflow state, delegation, quality gates | High | Dedicated |
| SHAKA | Architect | System design, architecture, ADRs, technical decisions | High | Dedicated |
| EDISON | Coding | Code, app development, tests, refactoring | High | Dedicated |
| PYTHAGORAS | Research | Research, comparison, evidence collection | Read-only | Dedicated |
| ATLAS | Security Advisor | Threat modeling, security review, risk mitigation | High review | Dedicated |
| YORK | QA / Reviewer | Review, QA, testing, verification | Review-only + test | Dedicated |
| LILITH | DevOps | CI/CD, deployment, infrastructure, rollback | High but gated | Dedicated |
| BONNEY | Documentation & RAG | Documentation, knowledge base, RAG ingestion | Docs + KB write | Dedicated |
| STUSSY | Monitoring & Ops | Monitoring, operations, incident triage, runbooks | Ops read + gated | Dedicated |

## Workflow Lifecycle (7 Phases + 6 Gates)

```text
Phase 0: Discovery    (Owner: KUMACHII/STELLA)  → Gate 0: Discovery Approved
Phase 1: Planning     (Owner: STELLA)           → Gate 1: PRD Approved
Phase 2: Solutioning  (Owner: SHAKA)            → Gate 2: Architecture Approved
Phase 3: Implementation (Owner: EDISON)         → Gate 3: Implementation Complete
Phase 4: Review       (Owner: YORK)             → Gate 4: Review Passed
Phase 5: Deploy       (Owner: LILITH)           → Gate 5: Ready to Deploy
Phase 6: Operate      (Owner: STUSSY)           → Gate 6: Operate Ready
```

Gate decisions: PASS / CONCERNS / FAIL
- PASS → next phase
- CONCERNS → proceed with monitoring
- FAIL → stuck, remediate before continuing

## Version History

| Version | Key Changes |
|---|---|
| v1.0.0 (Original) | HERMES-BMAD framework, 5-layer foundation, agent catalog |
| v1.0.0 (Updated) | Added KUMACHII/STELLA split, deployment modes, model strategy |
| v1.1.0 (Final Merged) | 6-layer foundation, operational control, RAG architecture, 7-phase lifecycle, full gate system |

The final v1.1.0 was created by merging the original and updated files, taking the strongest elements from each:
- Original: KUMACHII-first intake, runtime deployment model, testing checklist, memory model
- Updated: RAG architecture, operations, model strategy, quality gates, deployment modes

## Related Pages

- [[hymes-multi-agent-architecture]] - Implementation details for the Hermes runtime
- [[phase-5-migration-plan]] - Migration from standalone to framework v1.1.0
- [[agent-operating-guide]] - Master operating guide for all agents
- [[rag-architecture]] - RAG pipeline for BONNEY knowledge agent
- [[token-optimization]] - RTK, code-review-graph, and context management
