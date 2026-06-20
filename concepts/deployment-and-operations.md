---
title: Deployment and Operations
created: 2026-06-20
updated: 2026-06-20
type: concept
tags:
  - deployment
  - operations
  - e2e-testing
  - commissioning
  - lifecycle
sources:
  - DEPLOYMENT_SUMMARY.md
  - E2E_LIFECYCLE_TEST.md
  - E2E_LIFECYCLE_TEST_REPORT.md
  - hermes-maf-live-e2e-orchestration-remediation-2026-05-26.md
  - hermes-maf-live-e2e-test-plan-2026-05-26.md
  - maf-ai-e2e-report-20260526-220239.md
---

# Deployment and Operations

Complete deployment summary, E2E lifecycle testing, and operational procedures for the KUMACHII-STELLA framework v1.1.0.

## Deployment Status

**Status:** ALL STAGES + ALL SKILLS COMPLETE + MEMORY LAYER LIVE (~98/100 tasks)

### Timeline
| Date | Activity |
|------|----------|
| 2026-05-12 | Gap analysis. Decision: full migration to Hermes profiles. |
| 2026-05-13 | Phase 5.0 + Phase 5.1. KUMACHII + STELLA + Default deployed. |
| 2026-05-14 | EDISON + PYTHAGORAS verified. ATLAS + YORK deployed. LILITH + BONNEY + STUSSY deployed. |
| 2026-05-15 | SHAKA deployed. EnowXAI fallback removed. Custom skills created. |
| 2026-05-16 | Memory Layer (Honcho) deployed. 24/7 hardening complete. |

### Agent Roster (10 Agents + Default)
| # | Agent | Role | Platform | Toolset |
|---|-------|------|----------|---------|
| 0 | Default | Engineering Agent | Telegram + Discord | Full |
| 1 | KUMACHII | Personal Assistant + Intake | Telegram + Discord | file, web, browser, messaging, delegation, cronjob, image_gen, tts |
| 2 | STELLA | Orchestrator + Workflow Owner | Discord | file, terminal (limited), messaging, delegation, cronjob, web |
| 3 | EDISON | Coding Agent (TDD) | Discord | file, terminal, code_exec, messaging, delegation |
| 4 | PYTHAGORAS | Research Agent | Discord | file, web, browser, messaging |
| 5 | ATLAS | Security Review | Discord | file, terminal, web, messaging |
| 6 | YORK | QA / Code Review | Discord | file, terminal, web, messaging |
| 7 | LILITH | DevOps (Gated Deploy) | Discord | file, terminal, web, messaging, cronjob |
| 8 | BONNEY | Documentation + RAG | Discord | file, web, messaging |
| 9 | STUSSY | Monitoring + Operations | Discord | file, terminal, messaging, cronjob |
| 10 | SHAKA | Architect (ADRs) | Discord | file, terminal (limited), web, messaging |

### Systemd Services
```text
hermes-gateway.service          (default)
hermes-gateway-kumachii.service
hermes-gateway-stella.service
hermes-gateway-edison.service
hermes-gateway-pythagoras.service
hermes-gateway-atlas.service
hermes-gateway-york.service
hermes-gateway-lilith.service
hermes-gateway-bonney.service
hermes-gateway-stussy.service
hermes-gateway-shaka.service
```

## Custom Skills (29 Total)

### Stage A: Foundation Agents (10 skills)
- `kumachii-intake`, `kumachii-finance`, `kumachii-content`
- `stella-gate-enforcer`, `stella-delegation-router`, `stella-decision-log`
- `edison-story-impl`, `edison-atomic-commits`
- `pythagoras-research-report`, `pythagoras-comparison`

### Stage C: Control Layer (6 skills)
- `atlas-threat-model`, `atlas-security-review`, `atlas-block-release`
- `york-review-checklist`, `york-acceptance-validation`, `york-block-release`

### Stage D: Operate Layer (10 skills)
- `lilith-deployment`, `lilith-rollback`, `lilith-commissioning`
- `bonney-rag-pipeline`, `bonney-docs-standards`, `bonney-source-hierarchy`
- `stussy-health-check`, `stussy-incident-response`, `stussy-runbook`, `stussy-block-release`

### Stage E: Architecture (3 skills)
- `shaka-architecture`, `shaka-adr`, `shaka-readiness-check`

## E2E Lifecycle Test

### Test Task
Add a `/health` endpoint to a new tiny LAN service called `hermes-pingback`.

### Expected Phase Flow
| Phase | Owner | Deliverable | Gate |
|---|---|---|---|
| 0 Discovery | KUMACHII → STELLA | task-brief | Gate 0 |
| 1 Planning | STELLA | PRD | Gate 1 |
| 2 Solutioning | SHAKA + ATLAS | architecture | Gate 2 (HUMAN) |
| 3 Implementation | EDISON | code + tests | Gate 3 |
| 4 Review | YORK + ATLAS | review reports | Gate 4 (HUMAN) |
| 5 Deploy | LILITH | running service | Gate 5 (HUMAN) |
| 6 Operate | STUSSY | watchdog + runbook | Gate 6 |

### Success Criteria
- [ ] Task brief landed
- [ ] Each phase has gate decision document
- [ ] All 10 agents posted substantive messages
- [ ] EDISON commits with conventional format + TDD evidence
- [ ] YORK + ATLAS review reports reach PASS or CONCERNS
- [ ] LILITH deploy succeeds, healthcheck green
- [ ] STUSSY watchdog cronjob registered
- [ ] BONNEY runbook written
- [ ] Total duration ≤ 4 hours

## Live E2E Remediation

Failed live contracts from `MAF-AI-E2E-20260526-220239`:

| ID | Contract | Failure | Required Fix |
|---|---|---|---|
| ORCH-001 | Handoff brief payload | Long template-style messages | Minimal task-brief pointer |
| ORCH-002 | Per-task thread lifecycle | No thread creation | Programmatic thread creation |
| ORCH-003 | STELLA approval path | No Telegram approval | Telegram approval before phase transition |
| ORCH-004 | Closeout chain | No report-back to KUMACHII | STELLA → KUMACHII → User chain |
| ORCH-005 | Observer-only execution | Operator nudged sessions | No operator intervention after kickoff |

### Retest Acceptance Criteria
All must be true for PASS:
- Provider validation passes for all 10 agents
- Skill mapping passes for all 10 agents
- Kickoff creates task thread with thread ID
- Every phase handoff in task thread
- Every handoff outbound message is minimal
- STELLA requests phase approval through Telegram
- STELLA blocks phase progression without approval
- STELLA reports closeout to KUMACHII
- KUMACHII reports summary to User
- Operator does not intervene during active run

## Verification Results

### Smoke Tests (Direct Response)
| Agent | Response Time | API Calls | Result |
|-------|--------------|-----------|--------|
| KUMACHII | ~30s | 3-4 | [OK] |
| STELLA | ~45s | 5-6 | [OK] |
| EDISON | ~60s | 8-10 | [OK] |
| PYTHAGORAS | ~40s | 4-5 | [OK] |
| ATLAS | ~50s | 5-6 | [OK] |
| YORK | ~55s | 5-6 | [OK] |
| LILITH | 71.8s | 7 | [OK] |
| BONNEY | 48.2s | 5 | [OK] |
| STUSSY | 61.4s | 5 | [OK] |
| SHAKA | ~90s | 10 | [OK] |

### Role Isolation Tests
| Test | Agent | Result |
|------|-------|--------|
| Terminal access denied | BONNEY | [OK] Refused |
| Write access denied | PYTHAGORAS | [OK] Read-only respected |
| Orchestration denied | EDISON | [OK] Refused |
| Code execution denied | SHAKA | [OK] Design only |

## Issues Resolved

| Issue | Root Cause | Fix |
|-------|-----------|-----|
| STELLA executing code directly | Terminal access without delegation policy | Added Terminal Usage Policy |
| YORK doing deep security review | No boundary with ATLAS | Added "max 20% security" rule |
| STUSSY crash loop | Corrupt session + EnowXAI fallback down | Cleared sessions, removed EnowXAI |
| SHAKA not receiving messages | Wrong channel ID | Updated to SHAKA channel ID |

## Related Pages

- [[multi-agent-workflow-framework]] - Framework specification
- [[phase-5-migration-plan]] - Migration plan and stages
- [[honcho-memory-integration]] - Memory layer deployment
- [[skills-architecture]] - Skills adoption plan
