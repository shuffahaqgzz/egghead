---
title: "Runtime Integration"
created: 2026-06-20
updated: 2026-06-20
type: concept
tags: [hermes, runtime, profiles, tools, memory, integration]
sources: []
confidence: high
---

# Runtime Integration

Hermes Agent runtime mapping untuk [[egghead-framework]].

## Hermes Profile Mapping

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

**Status:** Model assignment per profile belum ditentukan.

## Communication Channels

| Channel | Purpose | Participants |
|---------|---------|--------------|
| Telegram (primary) | User-facing approvals, personal tasks | Intake ↔ User |
| Discord (inter-agent) | Agent-to-agent handoffs, status reports | All agents |
| Discord (approvals) | Gate approval prompts from Orchestrator | Orchestrator → User |

**Handoff Protocol:** Primary handoff mechanism via structured skill & gates principle. Discord used for ad-hoc communication.

## Hermes Tool Integration

| Hermes Tool | Egghead Usage |
|-------------|---------------|
| `delegate_task` | Subagent dispatch (per-agent delegation study needed) |
| `skill_view` | Load workflow and discipline skills |
| `session_search` | Recall past sessions and decisions |
| `hindsight_*` | Long-term memory for agent learning |
| `cronjob` | Background monitoring (case-dependent: cron + always-on) |
| `mcp_sequential_thinking` | Complex reasoning for architecture/planning |
| `todo` | Sprint tracking and story management |
| `execute_code` | Batch operations and analysis |

## Knowledge Architecture

**Memory Sharing:** Dual approach — Hindsight external memory provider + Knowledge base.

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

## Token Optimization

| Strategy | Description | Implementation |
|----------|-------------|----------------|
| **RTK** | Command output compression | `rtk-compression` skill |
| **Caveman** | Response compression | `caveman-output` skill |
| **Code Review Graph** | Blast radius analysis | `code-review-graph` skill |
| **Smart Zone** | Context optimization | Built into workflow |
| **Ponytail** | Ladder of laziness before coding | `ponytail` skill for Coder |
| **Subagent Isolation** | Fresh context per task | `delegate_task` usage |

## Cost Optimization

**Status:** Model assignment per phase/gate belum ditentukan.

**Future:** Setelah framework terimplementasi dan diuji, tentukan model optimal per phase berdasarkan empiris.
