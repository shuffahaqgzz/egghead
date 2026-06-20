---
title: Agent Roster
created: 2026-06-20
updated: 2026-06-20
type: concept
tags: [agent, multi-agent, coordinator, supervisor]
sources: [_archive/AGENTS.md, _archive/AGENT_DEPLOYMENT_TEMPLATE.md]
confidence: high
---

# Agent Roster

The KUMACHII-STELLA multi-agent system deploys 10 specialized agents, each with a single responsibility, defined access level, and escalation path. Agents communicate via Discord (inter-agent) and Telegram (user-facing approvals).

## Agent Roles

| Agent | Role | Platform | Access | Can Block |
|-------|------|----------|--------|-----------|
| KUMACHII | Personal assistant + intake | Telegram | Medium | No |
| STELLA | Orchestrator + workflow owner | Discord + Telegram | High | Yes (gates) |
| SHAKA | Architect (ADRs, system design) | Discord | High | No |
| EDISON | Coding (TDD executor) | Discord | High | No |
| PYTHAGORAS | Research (read-only) | Discord | Read-only | No |
| ATLAS | Security review | Discord | High review | Yes |
| YORK | QA / reviewer | Discord | Review + test | Yes |
| LILITH | DevOps (gated deploy) | Discord | High but gated | No |
| BONNEY | Documentation + RAG | Discord | Docs + KB write | No |
| STUSSY | Monitoring + operations | Discord | Ops read + gated | Yes |

## Delegation Matrix

| Task Type | Owner | Support | Reviewer | Approval Required |
|-----------|-------|---------|----------|-------------------|
| Personal task | KUMACHII | BONNEY | User | If sensitive |
| Project planning | STELLA | PYTHAGORAS, BONNEY | User | Yes |
| Architecture | SHAKA | PYTHAGORAS, ATLAS | STELLA | Yes |
| Coding | EDISON | SHAKA, BONNEY | YORK | For merge |
| Research | PYTHAGORAS | BONNEY | STELLA | If decision-impacting |
| Security review | ATLAS | SHAKA, LILITH | STELLA | Yes for exceptions |
| QA/testing | YORK | EDISON, ATLAS | STELLA | Yes for release |
| Deployment | LILITH | ATLAS, STUSSY | STELLA | Yes for production |
| Documentation | BONNEY | All agents | YORK | If source-of-truth changes |
| Monitoring/Ops | STUSSY | LILITH, ATLAS | STELLA | For remediation |

## Communication Routing

- **User-facing approvals** (Gates 0/1/2/4/5, risky decisions) → **Telegram** (`@stella_vgpunk_bot`)
- **Inter-agent comms** (handoffs, delegation, status, decision log) → **Discord**
- Mixing the two breaks audit trail and risks user missing approval prompts

## Agent Provisioning

Each agent runs as a dedicated [[deployment-pattern|Hermes profile]] with:
- Unique Discord bot token and channel
- Dedicated LLM model and provider
- Separate credentials, config, session, and memory
- Systemd service for lifecycle management

## Bot ID Reference

| Agent | Bot ID | Channel |
|-------|--------|---------|
| EDISON | 1501608989656482003 | 1501447521086476348 |
| PYTHAGORAS | 1501608874132897802 | 1501447723000266793 |
| ATLAS | 1501609076503740466 | 1501447906396344320 |
| STELLA | 1502701102083080282 | 1502667343279423619 |
| KUMACHII | 1485148050685956117 | 1502667177218539560 |
| SHAKA | 1500373431382704259 | 1502666915808542951 |
| LILITH | 1501609251716599909 | 1502667034532774041 |
| YORK | 1502701165765332992 | 1502667093571534958 |
| BONNEY | 1502701232949694535 | 1502926214413811792 |
| STUSSY | 1502739573636075723 | 1502926523789742191 |

## Related Concepts

- [[overall-architecture]] — How agents fit in the system
- [[gate-system]] — Phases each agent owns
- [[handoff-protocol]] — How agents communicate
- [[deployment-pattern]] — How agents are provisioned
