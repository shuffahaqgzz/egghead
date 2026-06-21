---
title: "Sprint Tracking Pattern"
created: 2026-06-20
updated: 2026-06-20
type: concept
tags: [sprint, tracking, stories, backlog, workflow]
sources: []
confidence: high
---

# Sprint Tracking Pattern

Sprint tracking integration untuk [[egghead-framework]], diadopsi dari BMAD method dan disesuaikan dengan Hermes runtime tools.

## Overview

BMAD menggunakan `sprint-status.yaml` untuk tracking. Egghead mengadopsi pola yang sama tapi menggunakan Hermes `todo` tool + file-based tracking.

## Sprint Status File

Setiap project punya sprint status file:

```yaml
# docs/sprint-status.yaml
sprint:
  id: "sprint-001"
  start: "2026-06-20"
  end: "2026-06-27"
  goal: "Implement authentication module"

epics:
  - id: "epic-1"
    name: "User Authentication"
    status: in-progress
    stories:
      - id: "story-1.1"
        name: "Login endpoint"
        status: done
        assignee: Coder
        reviewer: QA
        points: 3
        completed: "2026-06-21"

      - id: "story-1.2"
        name: "Registration endpoint"
        status: in-progress
        assignee: Coder
        reviewer: QA
        points: 5
        started: "2026-06-22"

      - id: "story-1.3"
        name: "Password reset"
        status: pending
        assignee: Coder
        reviewer: QA
        points: 3

  - id: "epic-2"
    name: "Authorization"
    status: pending
    stories:
      - id: "story-2.1"
        name: "Role-based access"
        status: pending
        assignee: Coder
        reviewer: QA
        points: 5

velocity:
  committed: 16
  completed: 3
  remaining: 13

action_items:
  - source: retrospective-001
    item: "Add integration tests for auth flow"
    status: open
```

## Story Status Flow

```
pending → in-progress → in-review → done
                  ↓           ↓
              blocked     changes-requested
                  ↓           ↓
              pending     in-progress
```

## Integration with Hermes Tools

### Using `todo` Tool

Sprint stories映射到 `todo` items:

```yaml
# Hermes todo list
todos:
  - id: "story-1.1"
    content: "[story] Login endpoint (3pts) → Coder → QA review"
    status: completed

  - id: "story-1.2"
    content: "[story] Registration endpoint (5pts) → Coder → QA review"
    status: in_progress

  - id: "story-1.3"
    content: "[story] Password reset (3pts) → Coder → QA review"
    status: pending
```

### Using `cronjob` Tool

Automated sprint tracking via cron:

```
Daily standup cron (every morning):
- Read sprint-status.yaml
- Count completed/in-progress/blocked stories
- Report velocity trend
- Alert on blocked stories > 24h

Sprint end cron:
- Calculate final velocity
- Generate retrospective prompt
- Archive completed stories
- Create next sprint template
```

## Sprint Ceremonies

### Sprint Planning (Phase 1 output)

1. Orchestrator reads PRD + architecture
2. Breaks into epics and stories
3. Estimates story points (Fibonacci: 1, 2, 3, 5, 8, 13)
4. Commits to sprint backlog
5. Creates sprint-status.yaml

### Daily Standup (Automated)

Cron job generates daily standup:

```markdown
# Daily Standup: [Date]

## Yesterday
- [story-X.Y] completed by [agent]
- [story-X.Z] in progress by [agent]

## Today
- [story-X.Z] continuing
- [story-X.W] starting

## Blockers
- [story-X.B] blocked by [reason]

## Velocity
- Committed: N points
- Completed: N points
- Days remaining: N
```

### Sprint Review (Phase 4 output)

1. QA reviews all completed stories
2. Demo working software
3. Collect feedback
4. Update sprint-status.yaml

### Retrospective (Post-sprint)

```markdown
# Retrospective: [Sprint ID]

## What went well
- [...]

## What could improve
- [...]

## Action items
- [ ] [Improvement with owner and deadline]
```

## Story Point Estimation

| Points | Description | Typical Duration |
|--------|-------------|-----------------|
| 1 | Trivial | < 30 min |
| 2 | Small, well-defined | 30 min - 1h |
| 3 | Medium, clear scope | 1-2h |
| 5 | Large, may need research | 2-4h |
| 8 | Very large, consider splitting | 4-8h |
| 13 | Epic, must split | 8h+ |

## Velocity Tracking

Track velocity across sprints to improve estimation:

```yaml
velocity_history:
  - sprint: "sprint-001"
    committed: 16
    completed: 13
    velocity: 81%

  - sprint: "sprint-002"
    committed: 15
    completed: 15
    velocity: 100%

  - sprint: "sprint-003"
    committed: 14
    completed: 12
    velocity: 86%

  average_velocity: 89%
```

## See Also

- [[workflow-lifecycle]] — Phase details
- [[egghead-framework]] — Framework overview
