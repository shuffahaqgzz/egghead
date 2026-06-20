# AI-Driven Multi-Agent Final Plan

Version: 1.0.0  
Status: Final deployment package  
Purpose: Operating plan and profile prompts for Gilang's AI-Driven Multi-Agent system.

## Source Mapping

| Source File | Used For |
|---|---|
| `hermes-discord-udpated.md` | SOUL.md behavior, communication, safety, tool discipline, persistence, verification |
| `kumachii-stella-multi-agent-workflow-framework-v1.1.0-final.md` | Agent roster, deployment architecture, profile isolation, lifecycle, gates, runtime model |
| `AI-Driven-Development-Workflow.md` | Development workflow: discovery, PRD, architecture, TDD, review, deployment |
| `code-review-graph-vs-rtk-analysis.md` | Token optimization: RTK, Caveman, code-review-graph, Smart Zone, command/context reduction |

## Final Architecture

```text
USER / OPERATOR
  |
  v
KUMACHII — Personal assistant and intake
  |
  v
STELLA — Orchestrator, workflow state owner, delegation controller, gate enforcer
  |
  +--> BUILD:   SHAKA, EDISON, PYTHAGORAS
  +--> CONTROL: ATLAS, YORK, BONNEY
  +--> OPERATE: LILITH, STUSSY
  |
  v
Knowledge / Tools / Runtime Layer
  Project docs, RAG, source repos, issue tracker, CI/CD, secrets manager,
  monitoring, RTK, Caveman, code-review-graph, LSP, AST-grep, MCP tools,
  isolated Hermes profiles.
```

Core separation:

```text
Personal/simple work -> KUMACHII handles directly.
Project/multi-phase work -> KUMACHII forwards a structured brief to STELLA.
STELLA controls delegation, gates, state, risk, and final synthesis.
Specialists execute or validate only inside their contracts.
```

## Required Profiles

| Profile | Agent | Role | Runtime Mode |
|---|---|---|---|
| `kumachii` | KUMACHII | Personal assistant / intake | Light or dedicated |
| `stella` | STELLA | Orchestrator / main agent | Dedicated |
| `shaka` | SHAKA | Architect | Dedicated |
| `edison` | EDISON | Coding | Dedicated |
| `pythagoras` | PYTHAGORAS | Research | Dedicated read-only |
| `atlas` | ATLAS | Security advisor | Dedicated review authority |
| `york` | YORK | QA / reviewer | Dedicated review/test |
| `lilith` | LILITH | DevOps | Dedicated gated execution |
| `bonney` | BONNEY | Documentation / RAG | Dedicated docs/KB |
| `stussy` | STUSSY | Monitoring / operations | Dedicated ops |

## File Deployment Layout

```text
~/.hermes/profiles/
├── kumachii/
│   ├── SOUL.md
│   └── AGENTS.md
├── stella/
│   ├── SOUL.md
│   └── AGENTS.md
├── shaka/
│   ├── SOUL.md
│   └── AGENTS.md
├── edison/
│   ├── SOUL.md
│   └── AGENTS.md
├── pythagoras/
│   ├── SOUL.md
│   └── AGENTS.md
├── atlas/
│   ├── SOUL.md
│   └── AGENTS.md
├── york/
│   ├── SOUL.md
│   └── AGENTS.md
├── lilith/
│   ├── SOUL.md
│   └── AGENTS.md
├── bonney/
│   ├── SOUL.md
│   └── AGENTS.md
└── stussy/
    ├── SOUL.md
    └── AGENTS.md
```

## Runtime Rules

- One profile = one isolated memory, credential set, session store, skill set, and gateway service.
- One Discord/Telegram bot token must not be reused by multiple gateways.
- Telegram should stay mainly on KUMACHII.
- Specialist agents should normally use Discord only.
- Credentials are copied intentionally, not assumed globally.
- Sensitive memory is not auto-shared; use handoff documents.

## Lifecycle

```text
0. Intake / Discovery
1. Planning
2. Solutioning / Architecture
3. Implementation
4. Review / QA / Security
5. Deployment
6. Operate / Monitor / Improve
```

No phase skipping. A project can move forward only when the relevant gate is PASS or the human owner explicitly accepts the risk.

## Token Optimization Policy

Use layered optimization:

1. Smart Zone: split work, focused context, phase-based reset.
2. RTK: compress command output from git, docker, kubectl, tests, logs.
3. Caveman/compact response: reduce repeated status and agent-to-agent chatter.
4. code-review-graph: use for codebases over 500 files, monorepos, blast-radius review, affected file selection.
5. LSP/AST-grep: use for deterministic symbol lookup and structural edits.
6. RAG: retrieve only relevant knowledge chunks; do not dump entire docs.

## Minimal Deployment Sequence

```text
1. Install Hermes runtime.
2. Create `kumachii` profile.
3. Configure KUMACHII model and platform.
4. Create `stella` profile.
5. Create specialist profiles.
6. Disable Telegram on specialist profiles.
7. Configure unique Discord token per profile.
8. Configure model/provider per profile.
9. Copy required credentials intentionally.
10. Install systemd services.
11. Start KUMACHII gateway.
12. Start STELLA gateway.
13. Start specialist gateways.
14. Run chat tests.
15. Run credential isolation tests.
16. Run token conflict checks.
17. Run end-to-end delegation test.
18. Document final status.
```

## End-to-End Delegation Test

Prompt:

```text
KUMACHII, ask STELLA to plan a secure personal finance dashboard. STELLA must delegate research to PYTHAGORAS, architecture to SHAKA, security review to ATLAS, QA checklist to YORK, documentation/RAG plan to BONNEY, deployment plan to LILITH, and monitoring/runbook plan to STUSSY.
```

Expected:

```text
KUMACHII creates intake.
STELLA creates task plan and gate map.
PYTHAGORAS returns research summary.
SHAKA returns architecture draft.
ATLAS returns security notes.
YORK returns QA checklist.
BONNEY returns docs/RAG outline.
LILITH returns deployment plan.
STUSSY returns monitoring/runbook plan.
STELLA consolidates result.
KUMACHII explains final result to user.
```
