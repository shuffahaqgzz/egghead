---
title: Phase 5 Migration Plan
created: 2026-06-20
updated: 2026-06-20
type: concept
tags:
  - migration
  - phase-5
  - framework
  - implementation
  - stages
sources:
  - PHASE_5_0_CONCEPTUAL_FIX_PLAN.md
  - MIGRATION_MAPPING.md
  - SPEC_AUDIT.md
  - STAGE_A_FOUNDATION_CHECKLIST.md
  - STAGE_C_D_E_TASK_PLAN.md
---

# Phase 5 Migration Plan

Master plan for migrating from standalone Flask/Python services to the KUMACHII-STELLA framework v1.1.0 with Hermes multi-profile runtime. This is the execution contract for full framework compliance.

## Decision Lock

| Topic | Decision | Source |
|---|---|---|
| Migration Strategy | **Opsi A — Full Framework Compliance** | Gilang, 2026-05-13 |
| Model Lifecycle | 6-layer framework + 7-phase + Gate 0-6 | Framework v1.1.0 |
| Runtime | Hermes multi-profile (10 dedicated) | Framework v1.1.0 Section 11 |
| Agent Roster | 10 agents (KUMACHII…STUSSY) | Framework v1.1.0 Section 5 |

## Migration Axes

Six migration axes:
1. **Runtime** — standalone Python service → Hermes multi-profile
2. **Role** — 2-agent (Kumachii + Stella) → 10-agent roster
3. **Workflow** — 3-tier routing → 7-phase + Gate 0-6
4. **Isolation** — centralized Redis → per-profile memory + STELLA state
5. **Handoff** — JSON schema → markdown/YAML contract
6. **Platform** — Flask HTTP + webhook → systemd + gateway + native Hermes transport

## Stage Roadmap

### Stage 5.0 — Conceptual Fix (1 day)
- Write plan documents
- Audit 7 spec files
- Create migration mapping
- Reframe names (kumachii-executor = EDISON, not KUMACHII)
- Draft AGENTS.md + RAG plan + Stage A checklist

### Stage 5.1 — Hermes Foundation (Stage A) — 2-3 days
- Create KUMACHII profile + Telegram config
- Create STELLA profile + Discord config
- Systemd services for both
- SOUL.md per profile
- Test isolation (chat, credential, token conflict)
- Test handoff markdown KUMACHII → STELLA

### Stage 5.2 — Migrate Existing Logic — 3-5 days
- `kumachii-executor` → reference library for EDISON
- `stella-agent` Flask → deprecate, logic to STELLA profile
- Redis role revised: STELLA state store only
- Memory layer migration to per-profile

### Stage 5.3 — Handoff + Gates Infrastructure — 2-3 days
- Markdown handoff template generator
- Gate 0-6 template files
- End-to-end test KUMACHII → STELLA handoff

### Stage 5.4 — Build Layer (Stage B) — 1 week
- EDISON profile + TDD skills
- PYTHAGORAS profile + research skills
- End-to-end delegation test

### Stage 5.5 — Control + Operate + Architect (Stage C, D, E) — 2-3 weeks
- Stage C: ATLAS + YORK
- Stage D: LILITH + BONNEY + STUSSY (BONNEY RAG online)
- Stage E: SHAKA

## Spec Audit Results

All 7 spec files need rework:

| File | Verdict | Target |
|---|---|---|
| INDEX.md | REVISE | 10-agent framework index |
| KUMACHII.md | REPURPOSE → EDISON.md | Role is coding executor |
| STELLA.md | REVISE (hard) | Orchestrator + 7-phase + Gate 0-6 |
| MEMORY_MODEL.md | REVISE | Per-profile memory + STELLA state |
| HANDOFF_PROTOCOL.md | DEPRECATE | JSON → markdown contract |
| DEPLOYMENT_GATE.md | REVISE | 3-tier → Gate 5 + risk metadata |
| QUICK_REFERENCE.md | DEPRECATE | Obsolete after index revision |

## Migration Mapping (Key Components)

### Runtime Migration
| Old | New | Phase |
|---|---|---|
| `/home/infra/projects/stella-agent/` (Flask) | `~/.hermes/profiles/stella/` (Hermes) | 5.1-5.2 |
| `/home/infra/projects/kumachii-executor/` | `~/.hermes/profiles/edison/` + reference library | 5.2-5.4 |
| `systemctl start stella-webhook.service` | `systemctl --user start hermes-gateway-stella.service` | 5.1 |

### Role Migration
| Function | Old Owner | New Owner |
|---|---|---|
| User intake | Stella | KUMACHII |
| Task classification | Stella | KUMACHII + STELLA |
| Delegation | Stella | STELLA (phase-based) |
| Execute + Verify | Kumachii | EDISON / PYTHAGORAS |
| Architecture | (implicit stella) | SHAKA |
| Security review | (none) | ATLAS |
| QA review | Kumachii verify | YORK |
| Deployment | Kumachii executor | LILITH |
| Documentation | (ad-hoc) | BONNEY |
| Monitoring | (none) | STUSSY |

## Stage A Foundation Checklist

### Pre-Flight
- [ ] PHASE_5_0_CONCEPTUAL_FIX_PLAN.md approved
- [ ] SPEC_AUDIT.md read
- [ ] MIGRATION_MAPPING.md read
- [ ] Backup old specs
- [ ] Git tag existing projects
- [ ] Identify Telegram bot token

### Profile Creation
- [ ] KUMACHII profile created with Telegram
- [ ] STELLA profile created with Discord
- [ ] Systemd services running
- [ ] SOUL.md installed per profile

### Isolation Tests
- [ ] Credential isolation verified
- [ ] No token conflicts
- [ ] Chat independence verified
- [ ] Memory isolation verified

### Handoff Test
- [ ] KUMACHII generates handoff markdown
- [ ] STELLA receives and processes handoff

## Stage C-D-E Task Plan

### Execution Order
```
Phase 1 — Enhance existing (Stage A-B):
  KUMACHII skills → STELLA skills → EDISON skills → PYTHAGORAS skills

Phase 2 — Stage C (Control Layer):
  ATLAS deploy + skills → YORK deploy + skills

Phase 3 — Stage D (Operate Layer):
  LILITH deploy + skills → BONNEY deploy + skills → STUSSY deploy + skills

Phase 4 — Stage E (Architect Layer):
  SHAKA deploy + skills
```

### Shared Skills (Framework Section 32)
| Skill | Used By | Purpose |
|---|---|---|
| `grill-me` | All agents | Self-review before handoff |
| `tdd` | EDISON, YORK | TDD enforcement |
| `security-review` | ATLAS, YORK, LILITH | Security review template |
| `qa-review` | YORK, STELLA | QA review template |
| `deployment-check` | LILITH, STELLA, STUSSY | Deployment checklist |
| `rag-ingestion` | BONNEY | RAG ingestion workflow |
| `handoff-template` | All agents | Standardized handoff format |
| `gate-checklist` | STELLA (primary) | Gate 0-6 templates |

## Related Pages

- [[multi-agent-workflow-framework]] - The framework v1.1.0 specification
- [[deployment-and-operations]] - Deployment summary and E2E testing
- [[skills-architecture]] - Skills adoption and propagation plan
