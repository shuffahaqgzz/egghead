---
title: "Monitoring & Observability Spec"
created: 2026-06-20
updated: 2026-06-20
type: concept
tags: [monitoring, observability, ops, alerts, metrics]
sources: []
confidence: high
---

# Monitoring & Observability Spec

Monitoring specification untuk [[egghead-framework]] runtime.

## What to Monitor

### 1. Agent Health

| Metric | Description | Alert Threshold |
|--------|-------------|-----------------|
| Agent response time | Time from request to first response | > 30s (warn), > 120s (critical) |
| Agent completion rate | % of tasks completed successfully | < 90% (warn), < 70% (critical) |
| Agent error rate | % of tasks that error out | > 5% (warn), > 15% (critical) |
| Session continuity | Crashes or unexpected session ends | > 0 (warn) |

### 2. Token Usage

| Metric | Description | Alert Threshold |
|--------|-------------|-----------------|
| Tokens per task | Total tokens consumed per task | > 100K (warn), > 500K (critical) |
| Cost per task | Dollar cost per task | > $5 (warn), > $20 (critical) |
| Daily spend | Total daily token cost | > $30 (warn), > $100 (critical) |
| Monthly spend | Total monthly token cost | > $500 (warn), > $2000 (critical) |
| Cache hit rate | % of prompts served from cache | < 30% (warn) |

### 3. Workflow Health

| Metric | Description | Alert Threshold |
|--------|-------------|-----------------|
| Gate pass rate | % of gates that PASS on first try | < 70% (warn), < 50% (critical) |
| Phase duration | Time spent per phase | > 2x estimated (warn) |
| Handoff success rate | % of handoffs completed without error | < 95% (warn) |
| Stuck tasks | Tasks in FAIL state for > 24h | > 0 (critical) |

### 4. Infrastructure

| Metric | Description | Alert Threshold |
|--------|-------------|-----------------|
| Hermes uptime | % time agent is available | < 99% (warn), < 95% (critical) |
| Memory usage | RAM consumption | > 80% (warn), > 95% (critical) |
| Disk usage | Storage consumption | > 80% (warn), > 90% (critical) |
| API provider status | Upstream provider health | Any outage (warn) |

## Monitoring Strategy

### Cron-Based Monitoring

Untuk metrics yang periodic:

```
┌─────────────────────────────────────────────┐
│ CRON: Every 5 minutes                       │
│ - Check agent health (ping)                 │
│ - Check token usage (last 5 min)            │
│ - Check disk/memory                         │
└─────────────────────────────────────────────┘

┌─────────────────────────────────────────────┐
│ CRON: Every hour                            │
│ - Aggregate hourly cost                     │
│ - Check gate pass rates                     │
│ - Check stuck tasks                         │
└─────────────────────────────────────────────┘

┌─────────────────────────────────────────────┐
│ CRON: Daily at 00:00                        │
│ - Daily cost summary                        │
│ - Task completion report                    │
│ - Performance trends                        │
└─────────────────────────────────────────────┘
```

### Always-On Monitoring

Untuk critical services:

- **Hermes Agent heartbeat** — Continuous health check
- **API provider fallback** — Monitor provider status, auto-switch on outage
- **Security alerts** — Immediate notification on security events

## Alert Channels

| Severity | Channel | Response Time |
|----------|---------|---------------|
| Info | Log only | No action needed |
| Warning | Discord #ops channel | Review within 4h |
| Critical | Telegram to Operator + Orchestrator | Response within 1h |
| Emergency | Telegram to User | Immediate |

## Incident Response

### Severity Levels

| Level | Description | Example | Response |
|-------|-------------|---------|----------|
| **P1 — Emergency** | Service down, data loss risk | Agent crash, API key exposed | Immediate fix, user notified |
| **P2 — Critical** | Major feature broken | Gate system failing, delegation broken | Fix within 4h |
| **P3 — Warning** | Degraded performance | Slow responses, high token usage | Fix within 24h |
| **P4 — Info** | Minor issue | Occasional timeout, cosmetic | Fix when convenient |

### Incident Report Template

```markdown
# Incident Report: [ID]

## Summary
[What happened]

## Timeline
- HH:MM — [Event]
- HH:MM — [Event]

## Impact
[What was affected, how many tasks/users]

## Root Cause
[Why it happened]

## Resolution
[How it was fixed]

## Prevention
[What to do to prevent recurrence]

## Action Items
- [ ] [Preventive measure]
- [ ] [Monitoring improvement]
```

## Dashboards

### Operations Dashboard

```
┌─────────────────────────────────────────┐
│ Agent Status                            │
│ Intake: ✅  Orchestrator: ✅            │
│ Architect: ✅  Coder: ✅               │
│ Researcher: ✅  Security: ✅           │
│ QA: ✅  DevOps: ✅                     │
│ Documenter: ✅  Operator: ✅           │
├─────────────────────────────────────────┤
│ Today's Cost: $X.XX / $Y.YY budget     │
│ [████████████░░░░] 65%                  │
├─────────────────────────────────────────┤
│ Active Tasks: N                         │
│ Gate Pass Rate: N%                      │
│ Avg Response Time: Ns                   │
└─────────────────────────────────────────┘
```

### Cost Dashboard

```
┌─────────────────────────────────────────┐
│ Monthly Cost Trend                      │
│ Week 1: $XXX  Week 2: $XXX             │
│ Week 3: $XXX  Week 4: $XXX             │
├─────────────────────────────────────────┤
│ Model Breakdown                         │
│ Haiku: 60% ($XX)                       │
│ Sonnet: 30% ($XX)                      │
│ Opus: 10% ($XX)                        │
├─────────────────────────────────────────┤
│ Optimization Savings                    │
│ Ponytail: -$XX                         │
│ RTK/Caveman: -$XX                      │
│ Cache hits: -$XX                       │
└─────────────────────────────────────────┘
```
