---
title: Human Approval Tool and Gate System
created: 2026-06-20
updated: 2026-06-20
type: concept
tags:
  - approval
  - gates
  - human-in-the-loop
  - telegram
  - implementation
sources:
  - V1_1_1_REQUEST_HUMAN_APPROVAL_SPEC.md
  - ai-driven-multi-agent-final-plan.md
---

# Human Approval Tool and Gate System

Implementation spec for the `request_human_approval` tool and the gate enforcement system used by STELLA for Phase 0-6 workflow control.

## Problem Statement

Current Gate 2/4/5 approvals require human (Gilang) to type text replies in Telegram → Hermes orchestrator forwards to STELLA Discord channel. Adds 30-90s latency per gate, prone to manual errors, and feels clunky on mobile.

Goal: STELLA calls `request_human_approval` tool → Telegram message with PASS / CONCERN / FAIL inline buttons → user taps → choice routed back to STELLA channel automatically.

## Design Decision

**Non-blocking pattern, NOT blocking.**

Why: blocking approval requires agent loop to suspend. STELLA should NOT suspend — she has other work, other tasks, other channels. She's an orchestrator.

Pattern: STELLA fires "I need approval" + button choices → returns immediately → user taps button → callback handler injects choice as text reply into STELLA's channel → STELLA picks up on next turn.

## Tool Schema

```python
TOOL_SCHEMA = {
    "name": "request_human_approval",
    "description": "Send interactive approval prompt with buttons",
    "parameters": {
        "title": "Short title (e.g., 'Gate 4 Approval — E2E-001')",
        "summary": "Multi-line markdown explaining what's being approved",
        "choices": ["PASS", "CONCERN", "FAIL"],  # max 6
        "forward_target": "platform:chat_id",  # e.g., "discord:1502667343279423619"
    }
}
```

## Implementation Steps

| Step | Component | Effort |
|---|---|---|
| 1 | New tool `request_human_approval` | 30 min |
| 2 | Telegram adapter `send_human_approval_buttons` | 30 min |
| 3 | Callback handler dispatch | 30 min |
| 4 | Approval state module `tools/user_approval.py` | 15 min |
| 5 | Tool registration | 10 min |
| 6 | STELLA SOUL.md hook | 15 min |
| 7 | Tests | 45 min |
| 8 | E2E smoke test | 30 min |
| **Total** | | **~3.5h** |

## Gate System

### Gate Definitions
| Phase | Gate | Decision | Human Required |
|---|---|---|---|
| 0 Discovery | Gate 0 | PASS / CONCERNS / FAIL | No (STELLA enforces) |
| 1 Planning | Gate 1 | PASS / CONCERNS / FAIL | No (STELLA enforces) |
| 2 Solutioning | Gate 2 | PASS / CONCERNS / FAIL | **Yes** |
| 3 Implementation | Gate 3 | PASS / CONCERNS / FAIL | No (automated tests) |
| 4 Review | Gate 4 | PASS / CONCERNS / FAIL | **Yes** |
| 5 Deploy | Gate 5 | PASS / CONCERNS / FAIL | **Yes** |
| 6 Operate | Gate 6 | PASS / CONCERNS / FAIL | No (STELLA enforces) |

### Gate Decision Rules
- **PASS** → advance to next phase
- **CONCERNS** → proceed with monitoring item
- **FAIL** → return to current phase with remediation

### Gate Document Template
```markdown
# Gate <N> Decision — <Task ID>

Phase: <phase_name>
Date: <timestamp>
Task: <task_id>

## Verdict
[PASS / CONCERNS / FAIL]

## Reviewer Input
- YORK: <verdict>
- ATLAS: <verdict>

## Human Approval
- Requested: <timestamp>
- Responded: <timestamp>
- Choice: <PASS/CONCERN/FAIL>
- User: Gilang

## Notes
<any additional context>
```

## Failure Modes

| Failure | Mitigation |
|---|---|
| User never clicks button | TTL 24h cleanup; task stays "PENDING APPROVAL" |
| Forward fails (Discord down) | Retry forward; log error; edit Telegram message |
| Multiple gates pending | Each has unique approval_id; independent resolution |
| Wrong user clicks | Authorization check enforced |
| User picks wrong choice | Add "Cancel" button; text reply override |

## Acceptance Criteria

- [ ] Button arrives at Telegram within 5s of tool call
- [ ] Button click registers within 2s of tap
- [ ] Choice forwards to Discord within 5s of click
- [ ] STELLA picks up choice within 30s of button tap
- [ ] TTL cleanup runs after 24h
- [ ] Authorization check rejects unauthorized users
- [ ] No regression in `send_exec_approval` shell command flow

## Agent Roster (Required Profiles)

| Profile | Agent | Role | Platform |
|---|---|---|---|
| `kumachii` | KUMACHII | Personal assistant / intake | Light or dedicated |
| `stella` | STELLA | Orchestrator / main agent | Dedicated |
| `shaka` | SHAKA | Architect | Dedicated |
| `edison` | EDISON | Coding | Dedicated |
| `pythagoras` | PYTHAGORAS | Research | Dedicated read-only |
| `atlas` | ATLAS | Security advisor | Dedicated review |
| `york` | YORK | QA / reviewer | Dedicated review/test |
| `lilith` | LILITH | DevOps | Dedicated gated |
| `bonney` | BONNEY | Documentation / RAG | Dedicated docs/KB |
| `stussy` | STUSSY | Monitoring / operations | Dedicated ops |

## Runtime Rules
- One profile = one isolated memory, credential set, session store, skill set, gateway service
- One Discord/Telegram bot token must not be reused
- Telegram stays mainly on KUMACHII
- Specialist agents use Discord only
- Credentials copied intentionally, not assumed globally

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

No phase skipping. Forward movement requires relevant gate PASS or human owner accepts risk.

## Related Pages

- [[multi-agent-workflow-framework]] - Framework specification
- [[agent-operating-guide]] - Agent roles and delegation
- [[deployment-and-operations]] - Deployment and E2E testing
