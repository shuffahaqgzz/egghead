---
title: "CONTEXT.md Template (Egghead MAF Adaptation)"
created: 2026-06-20
updated: 2026-06-20
type: concept
tags: [context, template, glossary, project, conventions]
sources: []
confidence: high
---

# CONTEXT.md Template — Egghead MAF

Adapted dari template asli oleh Gilang. Disesuaikan untuk [[egghead-framework]] dengan generic role names dan Hermes runtime.

**Usage:** Copy template ini ke project root sebagai `CONTEXT.md`. Fill placeholders untuk setiap project.

---

## Review & Adaptation Notes

### Yang dipertahankan dari template asli:
- **Section 0** (How agents use this file) — rules yang solid
- **Section 3** (Canonical glossary) — well-structured
- **Section 5** (Role boundaries) — clear owns/doesn't own
- **Section 6** (Routing map) — practical natural language routing
- **Section 8** (Source of truth files) — clear hierarchy
- **Section 14** (Quality gates) — gate checklists
- **Section 17** (Handoff state) — structured format
- **Section 19** (Security context) — good posture
- **Section 20** (Token optimization) — comprehensive

### Yang diubah:
1. **Agent names:** KUMACHII → Intake, STELLA → Orchestrator, dll (generic role names)
2. **Skills directory:** `.skills/` (MattPocock format) → `~/.hermes/skills/` (Hermes format)
3. **Platform channels:** Specific IDs → configurable placeholders
4. **Runtime paths:** Gilang-specific → generic template
5. **Architecture section:** Specific setup → generic template

### Yang ditambahkan:
1. **Skill loading protocol** — reference ke [[skill-architecture]]
2. **Cost tracking** — reference ke [[cost-estimation-model]]
3. **Sprint tracking** — reference ke [[sprint-tracking-pattern]]
4. **Multi-project context** — reference ke [[multi-project-support]]

---

## Template

```markdown
# CONTEXT.md — [PROJECT_NAME]

> Shared vocabulary, project memory ringan, dan context contract untuk semua agents.
> Update saat istilah, scope, architecture, commands, gates, atau decisions berubah.

---

## 0. How Agents Must Use This File

Agents must read this file when:
- starting a new project task,
- entering a new lifecycle phase,
- receiving ambiguous terminology,
- writing PRD/architecture/story/review docs,
- delegating to another agent,
- naming files/modules/features,
- explaining project state to owner,
- recovering after context compression.

Rules:
- Use the exact vocabulary defined here.
- Do not invent alternate names for the same concept.
- Add new terms when repeated 2+ times or when ambiguity appears.
- Keep project-specific facts here, not in SOUL.md.
- Do not store raw secrets.
- Mark uncertainty explicitly as `Unknown` or `Needs verification`.

---

## 1. Project Identity

```yaml
project_name: [PROJECT_NAME]
owner: [OWNER_NAME]
mission: [PROJECT_MISSION]
primary_repo: [PRIMARY_REPO]
project_root: [PROJECT_ROOT]
default_branch: [DEFAULT_BRANCH]
hermes_home: [HERMES_HOME]
timezone: [TIMEZONE]
primary_language: [LANGUAGE]
technical_language: English for code/log/API terms
```

---

## 2. Current Project Phase

```yaml
phase: [0-6]
phase_name: [Intake/Planning/Solutioning/Implementation/Review/Deploy/Operate]
gate_status: PASS/CONCERNS/FAIL/NOT_STARTED
last_gate_review: [YYYY-MM-DD]
owner_approval_status: approved/pending/not_required
```

Active objective:
```text
[Main outcome currently being pursued]
```

Non-goals:
```text
- [Intentionally out of scope]
- [Should not be changed]
```

---

## 3. Canonical Glossary

| Term | Meaning | Do not confuse with |
|------|---------|---------------------|
| Hermes | Runtime/agent system running profiles, gateways, tools, memory | Generic chatbot |
| Profile | Isolated Hermes runtime identity with own config, memory, credentials | Temporary sub-agent |
| Gateway | Platform service that exposes a profile (Telegram/Discord) | Model provider |
| Agent | Named role with stable identity and responsibility | Random helper prompt |
| Orchestrator | Agent that owns workflow state, routing, gates | Coding agent |
| Specialist | Agent with narrow role contract (Coder, Researcher, etc) | Orchestrator |
| Gate | Quality checkpoint before phase progression | Optional checklist |
| Handoff | Structured document passed between agents | Raw chat transcript |
| Smart Zone | Focused context/task scope where agent performs well | Dumping full context |
| Context Reset | Intentional reset at phase/review boundary | Losing memory |
| TDD | RED → GREEN → REFACTOR cycle | Writing tests after code |
| Vertical Slice | Data + logic + interface + test in one story | Horizontal layers |
| Ponytail | Ladder of laziness before coding | Reckless coding |
| RTK | Command output compression | Deleting logs |
| Caveman | Compact response style | Low-quality response |

---

## 4. Agent Roster

| Agent | Profile | Role | Platform | Status |
|-------|---------|------|----------|--------|
| Intake | `intake` | Personal assistant / intake | Telegram | active |
| Orchestrator | `orchestrator` | Workflow state / gates | Discord | active |
| Architect | `architect` | ADR / system design | Discord | active |
| Coder | `coder` | Implementation / tests / TDD | Discord | active |
| Researcher | `researcher` | Research / comparison / evidence | Discord | active |
| Security | `security` | Threat model / risk review | Discord | active |
| QA | `qa` | Review / validation | Discord | active |
| DevOps | `devops` | Deploy / rollback | Discord | active |
| Documenter | `documenter` | Docs / RAG / knowledge | Discord | active |
| Operator | `operator` | Monitoring / ops / incident | Discord | active |

---

## 5. Role Boundaries

| Role | Owns | Does not own |
|------|------|-------------|
| Intake | Initial request, clarification, routing | Project gate approval |
| Orchestrator | Task decomposition, delegation, gates, synthesis | Specialist execution |
| Architect | Architecture, ADR, boundaries, readiness | Large implementation |
| Coder | Implementation, tests, fixes, commits | Architecture redesign |
| Researcher | Source gathering, comparison, evidence | Final product decision |
| Security | Threat model, credential/access risk | Harmful exploit execution |
| QA | Test validation, review verdict | Approving own implementation |
| Documenter | README, runbook, RAG, handoff | Storing raw secrets |
| DevOps | Deploy, smoke test, rollback | Production deploy w/o approval |
| Operator | Logs, uptime, incidents | Destructive remediation w/o approval |

---

## 6. Routing Map

| User says | Route to | Expected output |
|-----------|----------|-----------------|
| "Research / compare / cari source…" | Researcher | Evidence, comparison |
| "Architecture / ADR / system design…" | Architect | Architecture draft, ADR |
| "Implement / fix bug / refactor…" | Coder | Code, tests, verification |
| "Review / audit / QA…" | QA | Issues by severity, verdict |
| "Threat model / security…" | Security | Risk matrix, mitigation |
| "Deploy / Docker / systemd…" | DevOps | Deploy plan, rollback |
| "Docs / README / runbook…" | Documenter | Markdown docs |
| "Monitor / logs / incident…" | Operator | Incident summary |
| "Coordinate all / full pipeline…" | Orchestrator | Plan, delegation |
| "Personal / simple task…" | Intake | Direct answer or brief |

---

## 7. Platform and Channel Map

```yaml
telegram_primary_channel: [TELEGRAM_CHANNEL]
discord_server_id: [DISCORD_SERVER_ID]
```

Per-agent channels (configure sesuai kebutuhan):

| Agent | Channel | Purpose |
|-------|---------|---------|
| Orchestrator | `#orchestrator-control` | Routing, gates, synthesis |
| Researcher | `#research-briefs` | Research notes |
| Architect | `#architecture` | Architecture, ADRs |
| Coder | `#dev-build` | Build logs, implementation |
| Security | `#security-review` | Threat model, review |
| QA | `#qa-review` | QA checklist, review |
| Documenter | `#docs-rag` | Docs, RAG updates |
| DevOps | `#deploy-ops` | Deployment, rollback |
| Operator | `#monitoring` | Uptime, incidents |

---

## 8. Source of Truth Files

| File | Location | Purpose |
|------|----------|---------|
| `AGENTS.md` | project root | Agent operating contract |
| `CONTEXT.md` | project root | Shared vocabulary and state |
| `SOUL.md` | `~/.hermes/profiles/[agent]/` | Agent identity |
| `docs/PRD.md` | `docs/` | Phase 1 artifact |
| `docs/architecture.md` | `docs/` | Architecture SOT |
| `docs/ADRs/*.md` | `docs/ADRs/` | Architecture decisions |
| `docs/epics/*.md` | `docs/epics/` | Epic/story breakdown |
| `docs/reviews/*.md` | `docs/reviews/` | Review reports |
| `docs/deployment.md` | `docs/` | Deployment procedure |
| `docs/runbook.md` | `docs/` | Operations runbook |
| `docs/sprint-status.yaml` | `docs/` | Sprint tracking |

---

## 9. Repository Structure

```text
[PROJECT_ROOT]/
├── AGENTS.md
├── CONTEXT.md
├── README.md
├── docs/
│   ├── PRD.md
│   ├── architecture.md
│   ├── deployment.md
│   ├── runbook.md
│   ├── sprint-status.yaml
│   ├── ADRs/
│   ├── epics/
│   └── reviews/
├── src/
├── tests/
└── .github/
```

Hermes skills (loaded from, not stored in project):
```text
~/.hermes/skills/
├── workflow/       # /grill, /plan, /architect, etc.
├── discipline/     # tdd, ponytail, diagnosing-bugs, etc.
├── agent/          # handoff, security-review, qa-review, etc.
└── optimization/   # rtk-compression, caveman-output, etc.
```

---

## 10. Runtime and Credential Context

```yaml
global_env: ~/.hermes/.env
profile_env: ~/.hermes/profiles/[agent]/.env
repo_env: [PROJECT_ROOT]/.env
```

Rules:
- Use configured credentials when available.
- Never print raw secret values.
- Use least privilege per profile.
- Do not commit `.env`, tokens, or private keys.

---

## 11. Current Architecture

```text
[Describe current architecture here]
```

---

## 12. Current Tech Stack

| Layer | Tool/Tech | Version | Status |
|-------|-----------|---------|--------|
| Runtime | Hermes Agent | [version] | [status] |
| Language | [Python/Node/etc] | [version] | [status] |
| Database | [SQLite/Postgres/etc] | [version] | [status] |
| CI/CD | [GitHub Actions/etc] | [version] | [status] |

---

## 13. Commands

```bash
# Project
[INSTALL_COMMAND]
[TEST_COMMAND]
[LINT_COMMAND]
[BUILD_COMMAND]

# Hermes
hermes profile list
hermes skills list
```

---

## 14. Quality Gates

Gate checklists ada di [[workflow-lifecycle]]. Copy gate template yang relevan:

```markdown
# Gate [N] — [Name]

- [ ] [Checklist item]
- [ ] [Checklist item]

Decision: PASS / CONCERNS / FAIL
Reviewer:
Date:
```

---

## 15. Decisions and ADR Index

| ADR | Title | Decision | Status |
|-----|-------|----------|--------|
| ADR-001 | [title] | [decision] | draft/accepted |

---

## 16. Current Backlog

| ID | Title | Phase | Owner | Priority | Status |
|----|-------|-------|-------|----------|--------|
| STORY-001 | [title] | [phase] | [agent] | [priority] | [status] |

---

## 17. Handoff State

```yaml
from: [agent]
to: [agent]
date: [YYYY-MM-DD]
task_id: [id]
status: pending/in_progress/blocked/completed
path: [file path]
```

---

## 18. Cost Tracking

Reference: [[cost-estimation-model]]

```yaml
monthly_budget: $[AMOUNT]
current_spend: $[AMOUNT]
optimization_active: [ponytail/rtk/caveman]
```

---

## 19. Security Context

```yaml
default_exposure: local/LAN/self-hosted
public_exposure: requires approval
secrets_in_docs: forbidden
production_mutation: requires approval
```

---

## 20. Token and Context Optimization

1. **Smart Zone** — split work into small focused tasks
2. **Phase reset** — reset context when moving phases
3. **RTK** — compress command outputs
4. **Caveman** — compact agent-to-agent chatter
5. **Ponytail** — ladder of laziness before coding

---

## 21. Naming Conventions

```text
docs/PRD.md
docs/architecture.md
docs/ADRs/NNN-short-title.md
docs/epics/epic-N-short-title.md
docs/reviews/YYYY-MM-DD_review.md
Branch: hermes/YYYYMMDD-short-task-name
```

---

## 22. Open Questions

| ID | Question | Owner | Status |
|----|----------|-------|--------|
| Q-001 | [question] | [owner] | open |

---

## 23. Known Risks

| Risk | Severity | Owner | Mitigation | Status |
|------|----------|-------|------------|--------|
| [risk] | [level] | [agent] | [mitigation] | open |

---

## 24. Change Log

| Date | Changed by | Change | Reason |
|------|-----------|--------|--------|
| [date] | [agent/user] | [change] | [reason] |

---

## 25. Maintenance Checklist

Weekly:
- [ ] Phase is correct
- [ ] Gate status current
- [ ] Agent roster current
- [ ] Commands work
- [ ] No secrets present
- [ ] Open questions reviewed
```
