---
title: Overall Architecture
created: 2026-06-20
updated: 2026-06-20
type: concept
tags: [architecture, multi-agent, orchestration, workflow, pipeline]
sources: [_archive/ARCHITECTURE.md, _archive/PRD.md, _archive/ADR-001-FRAMEWORK.md, _archive/AGENTS.md]
confidence: high
---

# Overall Architecture

The Egghead multi-agent framework implements a **phase-gated, role-separated workflow** where 10 specialized AI agents collaborate through structured handoffs. The system runs on Hermes Agent profiles communicating via Discord (inter-agent) and Telegram (user-facing approvals).

## Core Design Principles

1. **Phase-gated progression** — Work moves through 7 sequential phases (0–6), each requiring a quality gate to proceed.
2. **Role separation** — Each agent has a single responsibility with defined access levels and escalation paths.
3. **Defense in depth** — Security, QA, and ops agents have blocking authority; no single agent can push to production unilaterally.
4. **Minimal scope** — Services are deployed with the smallest viable stack (e.g., stdlib over frameworks when scope is trivial).

## Architecture Layers

### Application Layer
Concrete services like [[hermes-pingback]] follow a standard pattern:
- Single-purpose Python service (stdlib or minimal framework)
- Docker Compose deployment on a shared LAN network
- LAN-only binding, no public exposure
- Non-root, read-only filesystem, minimal privileges

### Agent Layer
The [[agent-roster]] provides specialized roles:
- **Intake:** KUMACHII (personal assistant)
- **Orchestration:** STELLA (workflow owner, gate approvals)
- **Architecture:** SHAKA (ADRs, system design)
- **Implementation:** EDISON (TDD executor)
- **Research:** PYTHAGORAS (read-only)
- **Security:** ATLAS (can block)
- **QA:** YORK (can block)
- **DevOps:** LILITH (gated deploy)
- **Documentation:** BONNEY (RAG + docs)
- **Operations:** STUSSY (monitoring, can block)

### Infrastructure Layer
- **Hermes Agent** — Gateway framework hosting agent profiles
- **Docker** — Container runtime for services
- **Discord** — Inter-agent communication bus
- **Telegram** — User-facing approval channel
- **notflix_network** — Docker bridge network for LAN services

## Data Flow

```
User → KUMACHII (intake) → STELLA (orchestrate)
  → SHAKA (design) → EDISON (implement) → YORK (review) → LILITH (deploy) → STUSSY (operate)
```

Gate decisions at each transition: **PASS** (proceed), **CONCERNS** (proceed with monitoring), **FAIL** (remediate).

## Key Decisions

- **ADR-001:** Python stdlib `http.server` over FastAPI/Flask for trivial services — zero dependencies, smallest attack surface, fastest cold start. See [[framework-selection-adr]].
- **LAN trust model:** Internal services skip authentication; container hardening replaces network-level auth.
- **Authority hierarchy:** Source-of-truth documents outrank generated summaries; architecture outranks PRD. See [[authority-hierarchy]].

## Related Concepts

- [[gate-system]] — How phases transition
- [[security-model]] — Container and network hardening
- [[deployment-pattern]] — Docker Compose and agent provisioning
- [[agent-roster]] — Agent roles and access levels
- [[coding-rules]] — Implementation standards
