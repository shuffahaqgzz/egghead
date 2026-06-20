---
title: Handoff Protocol
created: 2026-06-20
updated: 2026-06-20
type: concept
tags: [communication, inter-agent, handoff, delegation]
sources: [_archive/AGENTS.md]
confidence: high
---

# Handoff Protocol

The handoff protocol defines how agents transfer work between each other in the [[gate-system|phase-gated workflow]]. Every delegated task uses a structured markdown template to prevent ambiguity, hidden assumptions, and scope creep.

## Handoff Template

```markdown
# Agent Handoff

From:
To:
Task ID:
Phase:
Priority:
Objective:
Inputs:
Files touched:
Decisions made:
Assumptions:
Constraints:
Open risks:
Acceptance criteria:
Required review:
Next action:
Status: PASS / CONCERNS / BLOCKED
```

## Handoff Rules

| Rule | Reason |
|------|--------|
| No vague handoff | Receiver must understand exact scope |
| No missing file paths | Receiver must find all relevant files |
| No hidden assumptions | All context must be explicit |
| No silent scope expansion | Scope changes require re-evaluation |
| Blocked tasks return to STELLA | Orchestrator resolves blockers |
| Risky decisions require human approval | Cannot autonomously approve high-risk changes |

## Status Values

| Status | Meaning |
|--------|---------|
| **PASS** | Task completed successfully, ready for next phase |
| **CONCERNS** | Completed with noted issues, proceed with monitoring |
| **BLOCKED** | Cannot proceed, requires escalation to STELLA |

## Escalation Triggers

Stop work and escalate when:
- Requirement is unclear
- Scope changes mid-task
- New dependency is needed
- Architecture does not cover the case
- Security risk appears
- Test fails after repeated fixes
- Production may be affected
- Monitoring is missing for production change

## Example Flow

```
SHAKA → EDISON (handoff: architecture decisions, file paths, constraints)
  EDISON → YORK (handoff: code files, test results, decisions made)
    YORK → LILITH (handoff: review verdict, security findings)
      LILITH → STUSSY (handoff: deployment status, runbook updates)
```

Each arrow is a structured handoff with full context.

## Related Concepts

- [[agent-roster]] — Agents involved in handoffs
- [[gate-system]] — Gates that trigger handoffs
- [[coding-rules]] — Standards checked during handoff review
- [[authority-hierarchy]] — Source priority during handoff disputes
