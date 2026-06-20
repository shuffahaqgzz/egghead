# HERMES-BMAD Multi-Agent Workflow Framework

**Version:** 1.0.0  
**Purpose:** Compact operational documentation for designing, deploying, and operating a multi-agent AI workflow framework.  
**Target Use:** AI agent deployment, software delivery workflow, research workflow, security review workflow, documentation workflow, and infrastructure automation workflow.  
**Status:** Reference architecture and deployment guide.

---

## 1. Core Idea

This framework combines five layers:

1. **SOP Discipline** — alignment first, Smart Zone, vertical slicing, TDD, human review.
2. **BMAD Structure** — discovery, planning, solutioning, implementation, review, deployment.
3. **OMO-Style Orchestration** — orchestrator, specialist agents, background research, read-only consultation.
4. **Hermes Runtime Isolation** — profile-based agents, isolated credentials, isolated memory, isolated sessions.
5. **Token Optimization** — RTK, Caveman, code-review-graph, context reset, focused task scope.

The objective is simple:

> Build a multi-agent system that is structured, isolated, auditable, token-efficient, and safe to operate.

---

## 2. Design Principles

### 2.1 Alignment Before Execution

Do not start implementation from vague input.

Every major task begins with:

- intent clarification
- scope definition
- assumptions
- constraints
- expected output
- success criteria

### 2.2 Smart Zone Discipline

Agents perform better with small, focused context.

Rules:

- keep tasks small
- split work into vertical slices
- reset context between major phases
- use separate sessions for review
- avoid long unfocused conversations
- avoid giving the whole codebase unless required

### 2.3 Vertical Slicing

Avoid horizontal delivery like:

```text
Database first → API later → UI last
```

Use vertical slices:

```text
One story = data + logic + interface + test
```

Each slice must be independently testable.

### 2.4 Test-Driven Development

Default implementation cycle:

```text
RED → GREEN → REFACTOR
```

Rules:

- write failing test first
- implement minimum code to pass
- verify result
- refactor only when needed
- never delete failing tests to pass quality gate

### 2.5 Human-in-the-Loop

Human approval is mandatory for:

- scope changes
- architecture changes
- dependency additions
- security exceptions
- production deployment
- final QA

### 2.6 Documents as Contracts

Agents hand off work through documents.

Documents are not decoration. They are control points.

---

## 3. Reference Architecture

```text
┌──────────────────────────────────────────────────────────────┐
│                  MULTI-AGENT WORKFLOW SYSTEM                 │
├──────────────────────────────────────────────────────────────┤
│                                                              │
│  User / Operator                                             │
│        │                                                     │
│        ▼                                                     │
│  Orchestrator Agent                                          │
│        │                                                     │
│        ├── Analyst Agent                                     │
│        ├── PM Agent                                          │
│        ├── Architect Agent                                   │
│        ├── Coding Agent                                      │
│        ├── Research Agent                                    │
│        ├── Security Agent                                    │
│        ├── Reviewer Agent                                    │
│        ├── Documentation Agent                               │
│        └── DevOps Agent                                      │
│                                                              │
├──────────────────────────────────────────────────────────────┤
│ Runtime Layer                                                │
│                                                              │
│  Hermes Profiles                                             │
│  ├── default  → Orchestrator / main assistant                │
│  ├── coda     → Coding agent                                 │
│  ├── resa     → Research agent                               │
│  ├── sevi     → Security agent                               │
│  └── optional → Architect / Reviewer / DevOps agents         │
│                                                              │
├──────────────────────────────────────────────────────────────┤
│ Optimization Layer                                           │
│                                                              │
│  ├── RTK                → command output compression         │
│  ├── Caveman            → response compression               │
│  ├── code-review-graph  → code context and blast radius      │
│  ├── LSP / AST-grep     → deterministic code navigatio       │
│  └── Context reset      → prevent Dumb Zone                  │
│                                                              │
└──────────────────────────────────────────────────────────────┘
```

---

## 4. Agent Types

### 4.1 Light Agents

Light agents run inside an existing gateway or profile.

Use them for simple domain routing.

Examples:

| Agent | Purpose | Isolation Needed |
|---|---|---|
| OfficeOps | office/admin tasks | No |
| ContentOps | writing/content tasks | No |
| EduOps | learning tasks | No |
| FinanceOps | simple finance notes | No |
| DocOps | document formatting | No |

Use light agents when:

- no separate credential is needed
- no high-risk tool access is needed
- no dedicated model is needed
- memory isolation is not critical

### 4.2 Dedicated Profile Agents

Dedicated agents run as isolated profiles.

Use them for serious work.

| Agent | Purpose | Isolation Required |
|---|---|---|
| Orchestrator | task routing and gate control | Yes |
| Coding Agent | implementation and tests | Yes |
| Research Agent | analysis and references | Yes |
| Security Agent | threat modeling and security review | Yes |
| Architect Agent | system design and ADRs | Recommended |
| Reviewer Agent | independent code review | Recommended |
| DevOps Agent | deployment and rollback | Recommended |

Use dedicated agents when:

- credentials must be isolated
- memory must be isolated
- model must be specialized
- role is high risk
- auditability matters

---

## 5. Agent Role Definitions

### 5.1 Orchestrator Agent

**Purpose:** Own workflow state.

Responsibilities:

- understand user request
- classify task type
- determine current phase
- delegate work
- enforce quality gates
- prevent phase skipping
- request human approval when needed
- summarize progress
- maintain decision log

Must not:

- bypass quality gates
- approve its own risky changes
- silently change scope
- deploy without user approval

### 5.2 Analyst Agent

**Purpose:** Discovery and research preparation.

Responsibilities:

- clarify problem
- identify stakeholders
- collect constraints
- create product brief
- list risks and assumptions

Output:

- `docs/product-brief.md`
- research notes
- assumptions list

### 5.3 PM Agent

**Purpose:** Convert brief into requirements.

Responsibilities:

- create PRD
- define scope
- define user stories
- define acceptance criteria
- define non-functional requirements
- split work into epics and stories

Output:

- `docs/PRD.md`
- `docs/epics/*.md`

### 5.4 Architect Agent

**Purpose:** Define technical solution.

Responsibilities:

- create architecture
- define system boundaries
- define data flow
- define API contracts
- create ADRs
- run implementation readiness check

Output:

- `docs/architecture.md`
- `docs/ADRs/*.md`
- readiness report

### 5.5 Coding Agent

**Purpose:** Implement stories.

Responsibilities:

- read story and architecture
- write tests first
- implement minimal solution
- run tests
- refactor carefully
- create atomic commits

Must follow:

- TDD
- vertical slicing
- architecture boundaries
- no unapproved dependency addition

### 5.6 Research Agent

**Purpose:** Perform research and comparison.

Responsibilities:

- collect references
- compare options
- analyze frameworks
- summarize findings
- support architecture decisions

Must not:

- modify code
- make final architecture decision
- invent unsupported claims

### 5.7 Security Agent

**Purpose:** Security analysis.

Responsibilities:

- threat modeling
- credential review
- access control review
- dependency risk review
- deployment risk review
- security checklist validation

Output:

- security review report
- risk matrix
- mitigation plan

### 5.8 Reviewer Agent

**Purpose:** Independent validation.

Responsibilities:

- review code correctness
- check tests
- check architecture compliance
- check security issues
- check maintainability

Must run in separate context where possible.

### 5.9 Documentation Agent

**Purpose:** Maintain documentation.

Responsibilities:

- write user docs
- write technical docs
- update README
- update deployment docs
- update runbooks

### 5.10 DevOps Agent

**Purpose:** Build, deploy, verify, rollback.

Responsibilities:

- CI/CD configuration
- environment validation
- deployment procedure
- smoke tests
- rollback plan
- commissioning report

Must not deploy production without human approval.

---

## 6. Workflow Lifecycle

## Phase 0 — Discovery

**Goal:** Understand the task.

Inputs:

- user request
- existing context
- constraints

Activities:

1. run alignment session
2. ask required clarification
3. define scope
4. define out-of-scope
5. identify assumptions
6. create product brief

Outputs:

- `docs/product-brief.md`
- `CONTEXT.md`
- assumptions list

Gate 0 checklist:

- [ ] problem is clear
- [ ] user goal is clear
- [ ] scope is bounded
- [ ] assumptions are listed
- [ ] key terms are defined

---

## Phase 1 — Planning

**Goal:** Define what to build.

Inputs:

- product brief
- context document

Activities:

1. create PRD
2. define user stories
3. define acceptance criteria
4. define non-functional requirements
5. create UX spec if needed

Outputs:

- `docs/PRD.md`
- `docs/ux-spec.md` if UI exists

Gate 1 checklist:

- [ ] PRD exists
- [ ] each story has acceptance criteria
- [ ] non-functional requirements exist
- [ ] out-of-scope is listed
- [ ] success criteria are measurable

---

## Phase 2 — Solutioning

**Goal:** Define how to build it.

Inputs:

- PRD
- UX spec
- constraints

Activities:

1. create architecture document
2. define components
3. define data flow
4. define interfaces
5. define storage model
6. define security model
7. create ADRs
8. create epics and stories
9. run implementation readiness check

Outputs:

- `docs/architecture.md`
- `docs/ADRs/*.md`
- `docs/epics/*.md`
- readiness report

Gate 2 checklist:

- [ ] architecture exists
- [ ] ADRs exist for major decisions
- [ ] components are defined
- [ ] boundaries are clear
- [ ] security model is defined
- [ ] deployment model is defined
- [ ] implementation readiness is PASS

---

## Phase 3 — Implementation

**Goal:** Build story by story.

Inputs:

- approved architecture
- approved stories

Per-story workflow:

```text
1. Read story
2. Read related architecture section
3. Write failing test
4. Implement minimum code
5. Run test
6. Fix failure
7. Refactor only if needed
8. Run full relevant test set
9. Update docs if needed
10. Commit atomically
```

Outputs:

- source code
- tests
- story completion note
- commit reference

Gate 3 checklist:

- [ ] all planned stories are complete
- [ ] tests pass
- [ ] no skipped critical tests
- [ ] no hardcoded secrets
- [ ] no unauthorized dependency
- [ ] architecture boundaries are respected

---

## Phase 4 — Review and QA

**Goal:** Validate quality.

Inputs:

- implemented stories
- code diff
- test result

Activities:

1. independent code review
2. security review
3. architecture compliance check
4. test coverage check
5. documentation check
6. fix issues

Outputs:

- `docs/reviews/code-review-*.md`
- security review report
- fix summary

Gate 4 checklist:

- [ ] code review passed
- [ ] security review passed
- [ ] critical issues fixed
- [ ] docs updated
- [ ] human QA completed

---

## Phase 5 — Deploy and Operate

**Goal:** Release safely.

Inputs:

- reviewed code
- deployment plan

Activities:

1. build artifact
2. run pre-deploy checks
3. deploy to staging
4. run smoke tests
5. approve production deployment
6. deploy production
7. monitor logs
8. prepare rollback

Outputs:

- deployment report
- smoke test result
- rollback note

Gate 5 checklist:

- [ ] build passed
- [ ] staging smoke test passed
- [ ] production approval exists
- [ ] production smoke test passed
- [ ] rollback path exists
- [ ] monitoring is active

---

## 7. Required Project File Structure

```text
project/
├── AGENTS.md
├── CONTEXT.md
├── README.md
├── .skills/
│   ├── grill-me.md
│   ├── grill-with-docs.md
│   ├── tdd.md
│   ├── diagnose.md
│   ├── prototype.md
│   ├── zoom-out.md
│   └── karpathy-principles.md
├── docs/
│   ├── product-brief.md
│   ├── PRD.md
│   ├── ux-spec.md
│   ├── architecture.md
│   ├── deployment.md
│   ├── runbook.md
│   ├── ADRs/
│   │   └── 000-template.md
│   ├── epics/
│   │   └── epic-000-template.md
│   └── reviews/
│       └── review-000-template.md
├── src/
├── tests/
├── scripts/
└── .github/
```

---

## 8. Hermes Runtime File Structure

```text
~/.hermes/
├── auth.json
├── config.yaml
├── .env
├── hermes-agent/
├── profiles/
│   ├── coda/
│   │   ├── auth.json
│   │   ├── config.yaml
│   │   ├── .env
│   │   ├── memory/
│   │   ├── sessions/
│   │   └── skills/
│   ├── resa/
│   │   ├── auth.json
│   │   ├── config.yaml
│   │   ├── .env
│   │   ├── memory/
│   │   ├── sessions/
│   │   └── skills/
│   └── sevi/
│       ├── auth.json
│       ├── config.yaml
│       ├── .env
│       ├── memory/
│       ├── sessions/
│       └── skills/
└── cron/
```

Rules:

- each profile has isolated config
- each profile has isolated credentials
- each profile has isolated memory
- each profile has isolated sessions
- each profile has isolated skills
- credentials are not assumed to be shared

---

## 9. Profile Mapping

Recommended minimum setup:

| Profile | Agent | Purpose | Platform | Isolation |
|---|---|---|---|---|
| default | Orchestrator | main routing and general assistant | Telegram + Discord | required |
| coda | Coding Agent | implementation and tests | Discord only | required |
| resa | Research Agent | research and analysis | Discord only | required |
| sevi | Security Agent | security review | Discord only | required |

Optional expansion:

| Profile | Agent | Purpose |
|---|---|---|
| arch | Architect Agent | architecture and ADR |
| revi | Reviewer Agent | independent review |
| devo | DevOps Agent | deployment and commissioning |
| doca | Documentation Agent | technical and user docs |

---

## 10. Gateway Rules

### 10.1 Token Rule

One bot token can be used by one gateway only.

Do not reuse the same Telegram or Discord token across profiles.

### 10.2 Telegram Rule

Use Telegram only for the default profile unless there is a clear reason.

Dedicated agents should normally use Discord only.

### 10.3 Environment Variable Rule

Do not set unused token variables to empty strings.

Use comments instead:

```bash
# TELEGRAM_BOT_TOKEN=
# TELEGRAM_ALLOWED_USERS=
# TELEGRAM_HOME_CHANNEL=
```

### 10.4 Provider Switching Rule

When switching model provider:

```bash
<profile> config set model.base_url ""
<profile> config set model.api_key ""
```

This avoids stale provider configuration.

---

## 11. Systemd Service Template

```ini
[Unit]
Description=Hermes Agent Gateway - <profile>
After=network.target

[Service]
Type=simple
User=infra
Environment=PYTHONUNBUFFERED=1
ExecStart=/home/infra/.hermes/hermes-agent/venv/bin/python -m hermes_cli.main --profile <profile> gateway run --replace
Restart=always
RestartSec=5
StandardOutput=journal
StandardError=journal

[Install]
WantedBy=default.target
```

Service names:

```text
hermes-gateway-default.service
hermes-gateway-coda.service
hermes-gateway-resa.service
hermes-gateway-sevi.service
```

---

## 12. Standard Commands

### 12.1 Profile Check

```bash
hermes profile list
```

### 12.2 Gateway Status

```bash
<profile> gateway status
systemctl --user status hermes-gateway-<profile>.service
```

### 12.3 Gateway Logs

```bash
journalctl --user -u hermes-gateway-<profile>.service -f
journalctl --user -u hermes-gateway-<profile>.service --since "1 hour ago" --no-pager
```

### 12.4 Restart Gateway

```bash
systemctl --user restart hermes-gateway-<profile>.service
```

### 12.5 Test Chat

```bash
<profile> chat -q "ping"
```

### 12.6 Verify Credentials

```bash
cat ~/.hermes/profiles/<profile>/auth.json | python3 -c "import sys,json; d=json.load(sys.stdin); print(list(d.get('credential_pool',{}).keys()))"
```

---

## 13. Commissioning Checklist

### 13.1 Infrastructure

- [ ] server accessible
- [ ] SSH working
- [ ] Python 3.11+ available
- [ ] systemd user service available
- [ ] network to provider available
- [ ] enough RAM available

### 13.2 Hermes Runtime

- [ ] Hermes installed
- [ ] virtual environment available
- [ ] CLI works
- [ ] default profile exists
- [ ] dedicated profiles created

### 13.3 Configuration

- [ ] global config exists
- [ ] global auth exists
- [ ] global `.env` exists
- [ ] profile config exists
- [ ] profile auth exists
- [ ] profile `.env` exists
- [ ] credentials copied intentionally

### 13.4 Platform

- [ ] Telegram token only used by default
- [ ] Discord token unique per profile
- [ ] channel IDs configured
- [ ] bot permissions validated

### 13.5 Service

- [ ] systemd services installed
- [ ] services enabled
- [ ] services started
- [ ] restart policy active
- [ ] logs clean

---

## 14. Testing Checklist

### 14.1 Gateway Test

```bash
systemctl --user status hermes-gateway-default.service
systemctl --user status hermes-gateway-coda.service
systemctl --user status hermes-gateway-resa.service
systemctl --user status hermes-gateway-sevi.service
```

Expected:

```text
Active: active (running)
```

### 14.2 Chat Test

```bash
hermes chat -q "ping"
coda chat -q "ping - respond with CODA online"
resa chat -q "ping - respond with RESA online"
sevi chat -q "ping - respond with SEVI online"
```

### 14.3 Model Test

```bash
coda chat -q "what model are you using? respond with model name only"
resa chat -q "what model are you using? respond with model name only"
sevi chat -q "what model are you using? respond with model name only"
```

### 14.4 Credential Isolation Test

```bash
cat ~/.hermes/profiles/coda/auth.json | grep -i credential_pool
cat ~/.hermes/profiles/resa/auth.json | grep -i credential_pool
cat ~/.hermes/profiles/sevi/auth.json | grep -i credential_pool
```

Expected:

- coda has only coding provider credentials
- resa has only research provider credentials
- sevi has only security provider credentials

### 14.5 Token Conflict Test

Check logs:

```bash
journalctl --user -u hermes-gateway-coda.service --since "10 minutes ago" --no-pager | grep -i "token"
```

Expected:

```text
No token already in use error
```

---

## 15. Token Optimization Strategy

### 15.1 RTK

Use for command output compression.

Best for:

- `git status`
- `git diff`
- `git log`
- `docker ps`
- `kubectl get`
- `npm test`
- `cargo test`

### 15.2 Caveman

Use for compact agent responses.

Best for:

- repeated status reports
- long-running coding sessions
- high-frequency agent communication

### 15.3 code-review-graph

Use for large codebases.

Best for:

- monorepo review
- blast radius analysis
- dependency mapping
- affected file selection
- code review context reduction

Recommended when:

- project has more than 500 files
- multi-file changes are common
- code review consumes many tokens
- architecture is complex

### 15.4 Context Reset

Reset context when:

- phase changes
- review starts
- agent becomes repetitive
- context is too large
- output quality drops
- assumptions become unclear

---

## 16. Quality Gate Templates

### Gate 0 — Discovery Approved

```markdown
# Gate 0 — Discovery Approved

- [ ] Problem is clear
- [ ] Goal is clear
- [ ] Scope is bounded
- [ ] Out-of-scope is listed
- [ ] Assumptions are listed
- [ ] Key terms are defined in CONTEXT.md

Decision: PASS / CONCERNS / FAIL
Reviewer:
Date:
Notes:
```

### Gate 1 — PRD Approved

```markdown
# Gate 1 — PRD Approved

- [ ] PRD exists
- [ ] User stories exist
- [ ] Acceptance criteria exist
- [ ] NFRs exist
- [ ] Success metrics exist
- [ ] Scope is stable

Decision: PASS / CONCERNS / FAIL
Reviewer:
Date:
Notes:
```

### Gate 2 — Architecture Approved

```markdown
# Gate 2 — Architecture Approved

- [ ] Architecture document exists
- [ ] ADRs exist
- [ ] Component boundaries are clear
- [ ] Data flow is clear
- [ ] API contracts are defined
- [ ] Security model is defined
- [ ] Deployment model is defined
- [ ] Implementation readiness is PASS

Decision: PASS / CONCERNS / FAIL
Reviewer:
Date:
Notes:
```

### Gate 3 — Implementation Complete

```markdown
# Gate 3 — Implementation Complete

- [ ] All planned stories complete
- [ ] Tests pass
- [ ] No critical skipped tests
- [ ] Code follows architecture
- [ ] No hardcoded secrets
- [ ] No unapproved dependency
- [ ] Docs updated where needed

Decision: PASS / CONCERNS / FAIL
Reviewer:
Date:
Notes:
```

### Gate 4 — Review Passed

```markdown
# Gate 4 — Review Passed

- [ ] Code review passed
- [ ] Security review passed
- [ ] Critical issues fixed
- [ ] Regression tests pass
- [ ] Documentation updated
- [ ] Human QA completed

Decision: PASS / CONCERNS / FAIL
Reviewer:
Date:
Notes:
```

### Gate 5 — Ready to Deploy

```markdown
# Gate 5 — Ready to Deploy

- [ ] Build passed
- [ ] Staging deployment passed
- [ ] Smoke test passed
- [ ] Rollback path exists
- [ ] Monitoring active
- [ ] Production approval exists

Decision: PASS / CONCERNS / FAIL
Reviewer:
Date:
Notes:
```

---

## 17. AGENTS.md Operating Guide

Copy this into project `AGENTS.md`.

```markdown
# AI Agent Operating Guide

## Identity

You are an AI software delivery agent working inside a phase-gated multi-agent workflow.

You must prioritize:

1. alignment
2. correctness
3. security
4. simplicity
5. testability
6. maintainability

## Before Any Task

1. Identify current phase.
2. Check required documents.
3. Check whether scope is clear.
4. Check whether a skill applies.
5. Ask for clarification if execution would be unsafe.
6. Do not skip quality gates.

## Phase Rules

| Condition | Phase | Required Action |
|---|---|---|
| No brief exists | Discovery | create product brief |
| No PRD exists | Planning | create PRD |
| No architecture exists | Solutioning | create architecture |
| Stories exist and architecture approved | Implementation | implement story with TDD |
| Code implemented | Review | run review and fix issues |
| Review passed | Deploy | deploy with approval |

## Coding Rules

- Write tests first.
- Make small changes.
- Verify after each change.
- Keep code runnable.
- Prefer simple code.
- Avoid premature abstraction.
- Do not add dependencies without approval.
- Do not modify unrelated files.
- Do not hardcode secrets.
- Do not bypass architecture boundaries.

## Review Rules

Check:

- correctness
- tests
- edge cases
- security
- architecture compliance
- naming consistency
- unnecessary complexity
- documentation impact

## Escalation Rules

Stop and escalate when:

- requirement is unclear
- scope changes
- dependency is needed
- architecture does not cover the case
- security risk appears
- test fails after repeated fixes
- deployment can affect production

## Output Rules

Use compact, direct sentences.
Do not bluff.
State uncertainty clearly.
Show commands when useful.
Show file paths when relevant.
Summarize decisions and next actions.
```

---

## 18. Security Model

### 18.1 Isolation

Each dedicated agent must have:

- isolated profile
- isolated credentials
- isolated memory
- isolated sessions
- isolated skills
- dedicated gateway service

### 18.2 Credential Handling

Rules:

- never commit credentials
- never place raw secrets in prompt files
- use `.env` or secret manager
- copy credentials intentionally
- rotate credentials when leaked
- keep least privilege per profile

### 18.3 Platform Access

Rules:

- default profile may use Telegram and Discord
- dedicated agents should use Discord only by default
- one token per bot
- one bot per dedicated profile
- restrict channels per bot

### 18.4 Tool Access

Rules:

- research agents should be read-only
- Oracle should be read-only
- coding agent can modify code
- DevOps agent can deploy only after approval
- security agent can block release

---

## 19. Troubleshooting

### 19.1 Gateway Will Not Start

Check logs:

```bash
journalctl --user -u hermes-gateway-<profile>.service -l --no-pager
```

Common causes:

- token conflict
- missing credential
- wrong provider config
- bad `.env`
- systemd user session issue

### 19.2 Token Already in Use

Cause:

```text
same bot token used by multiple gateways
```

Fix:

- disable platform on secondary profile
- use unique token per profile
- restart affected service

### 19.3 Chat Timeout

Check:

```bash
<profile> gateway health
<profile> config get model.provider
<profile> config get model.default
cat ~/.hermes/profiles/<profile>/auth.json
```

### 19.4 Wrong Model Used

Fix:

```bash
<profile> config set model.base_url ""
<profile> config set model.api_key ""
systemctl --user restart hermes-gateway-<profile>.service
```

### 19.5 Credential Not Found

Check:

```bash
cat ~/.hermes/profiles/<profile>/auth.json | python3 -m json.tool
```

Fix:

- copy required provider credential into profile `auth.json`
- restart gateway

---

## 20. Rollback

### 20.1 Stop One Agent

```bash
systemctl --user stop hermes-gateway-<profile>.service
systemctl --user disable hermes-gateway-<profile>.service
```

### 20.2 Restart One Agent

```bash
systemctl --user restart hermes-gateway-<profile>.service
```

### 20.3 Full Rollback to Default Only

```bash
systemctl --user stop hermes-gateway-coda.service
systemctl --user stop hermes-gateway-resa.service
systemctl --user stop hermes-gateway-sevi.service

systemctl --user disable hermes-gateway-coda.service
systemctl --user disable hermes-gateway-resa.service
systemctl --user disable hermes-gateway-sevi.service

systemctl --user restart hermes-gateway-default.service
```

---

## 21. Minimal Deployment Plan

Use this when deploying from scratch.

```text
1. Install Hermes runtime
2. Create default profile
3. Configure default model and platform
4. Create coda, resa, sevi profiles
5. Disable Telegram on dedicated profiles
6. Configure unique Discord token per profile
7. Configure model per profile
8. Copy required credentials per profile
9. Install systemd services
10. Start default gateway
11. Start dedicated gateways
12. Run chat tests
13. Run credential isolation tests
14. Run token conflict checks
15. Document final status
```

---

## 22. Recommended Initial Agent Deployment

### default

```yaml
role: Orchestrator
purpose: Main assistant, routing, quality gates
platforms:
  telegram: enabled
  discord: enabled
```

### coda

```yaml
role: Coding Agent
purpose: Code, tests, refactor
platforms:
  telegram: disabled
  discord: enabled
```

### resa

```yaml
role: Research Agent
purpose: Research, comparison, analysis
platforms:
  telegram: disabled
  discord: enabled
```

### sevi

```yaml
role: Security Agent
purpose: Security review, threat modeling
platforms:
  telegram: disabled
  discord: enabled
```

---

## 23. Operating Rules Summary

```text
- Orchestrator owns workflow state.
- Documents are contracts.
- No phase skipping.
- No implementation before architecture approval.
- No production deploy without human approval.
- Developer follows TDD.
- Reviewer runs independently.
- Security can block release.
- Research and Oracle are read-only.
- Context must stay small and focused.
- Token optimization is mandatory for large tasks.
```

---

## 24. Definition of Done

A task is done only when:

- requirement is satisfied
- tests pass
- review passed
- security checked if relevant
- docs updated if relevant
- deployment verified if relevant
- user-facing summary is provided

A project is done only when:

- all planned stories are complete
- all gates pass
- deployment works
- rollback exists
- runbook exists
- human owner accepts result

---

## 25. Final Notes

This framework is designed for practical operation.

Keep it strict where risk is high.
Keep it lightweight where risk is low.
Use dedicated agents for isolation.
Use light agents for simple routing.
Use documents for handoff.
Use quality gates for control.
Use humans for final judgment.

