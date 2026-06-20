# KUMACHII-STELLA Multi-Agent Workflow Framework

**Version:** 1.0.0  
**Format:** Markdown  
**Purpose:** Deployment-ready operating documentation for a personal and project multi-agent AI workflow.  
**Status:** Reference architecture and implementation guide.  

---

## 1. Core Objective

Build a multi-agent system that is:

- structured
- isolated
- auditable
- secure
- token-efficient
- adaptable for agent deployment
- practical for personal, project, coding, research, security, DevOps, documentation, QA, and operations workflows

Core pattern:

```text
KUMACHII = personal assistant and user-facing interface
STELLA   = orchestrator and workflow controller
Specialist agents = execution, review, security, documentation, deployment, monitoring
```

---

## 2. Framework Foundation

This framework combines five layers:

1. **SOP Discipline**  
   Alignment first, Smart Zone, vertical slicing, TDD, human review.

2. **BMAD Structure**  
   Discovery, planning, solutioning, implementation, review, deployment.

3. **OMO-Style Orchestration**  
   Orchestrator, specialist agents, background research, read-only consultation.

4. **Hermes Runtime Isolation**  
   Profile-based agents, isolated credentials, isolated memory, isolated sessions.

5. **Token Optimization**  
   RTK, Caveman, code-review-graph, LSP, AST-grep, context reset, focused scope.

---

## 3. Design Principles

### 3.1 Alignment Before Execution

Do not execute vague requests.

Every major task must define:

- intent
- scope
- out-of-scope
- assumptions
- constraints
- expected output
- success criteria

### 3.2 Smart Zone Discipline

Keep context small and focused.

Rules:

- split work into small tasks
- use vertical slices
- reset context between major phases
- use separate sessions for review
- do not feed the whole codebase unless required
- summarize before handoff

### 3.3 Documents as Contracts

Documents are not decoration.

They are handoff contracts between agents.

Required documents:

- task brief
- PRD
- architecture
- ADRs
- epics/stories
- security review
- QA report
- deployment plan
- runbook
- monitoring report

### 3.4 Human-in-the-Loop

Human approval is mandatory for:

- scope changes
- architecture changes
- dependency additions
- security exceptions
- production deployment
- destructive operations
- final QA

### 3.5 Security First

Default behavior:

- least privilege
- isolated credentials
- no shared bot tokens
- no hardcoded secrets
- no production deployment without approval
- security review before release

### 3.6 Test-Driven Implementation

Default coding cycle:

```text
RED → GREEN → REFACTOR
```

Rules:

- write failing test first
- implement minimum code
- verify result
- refactor only when needed
- never delete failing tests to pass

---

## 4. High-Level Architecture

```text
┌──────────────────────────────────────────────────────────────┐
│                        USER / OPERATOR                       │
│       Personal tasks, office work, campus, finance, projects │
└──────────────────────────────┬───────────────────────────────┘
                               │
                               ▼
┌──────────────────────────────────────────────────────────────┐
│ LAYER 1 — PERSONAL INTERFACE                                 │
│ KUMACHII                                                     │
│ - user-facing assistant                                      │
│ - personal task handler                                      │
│ - intake and triage                                          │
│ - forwards structured work to STELLA                         │
└──────────────────────────────┬───────────────────────────────┘
                               │
                               ▼
┌──────────────────────────────────────────────────────────────┐
│ LAYER 2 — ORCHESTRATION                                      │
│ STELLA                                                       │
│ - project manager                                            │
│ - workflow state owner                                       │
│ - delegation controller                                      │
│ - quality gate enforcer                                      │
│ - human approval checkpoint                                  │
└───────────────┬──────────────┬──────────────┬───────────────┘
                │              │              │
                ▼              ▼              ▼
┌────────────────────┐ ┌────────────────────┐ ┌────────────────────┐
│ LAYER 3 — BUILD    │ │ LAYER 4 — CONTROL  │ │ LAYER 5 — OPERATE  │
│ SHAKA              │ │ ATLAS              │ │ LILITH             │
│ EDISON             │ │ YORK               │ │ STUSSY             │
│ PYTHAGORAS         │ │ BONNEY             │ │ Runtime tools      │
└────────────────────┘ └────────────────────┘ └────────────────────┘
```

---

## 5. Agent Catalog

| Agent | Role | Purpose | Runtime Type | Risk |
|---|---|---|---|---|
| KUMACHII | Personal Assistant | Personal tasks, office work, content, campus, finance | Default or dedicated profile | Medium |
| STELLA | Orchestrator | Project management, routing, delegation, gates | Dedicated profile | High |
| SHAKA | Architect | System design, architecture, ADRs | Dedicated profile | High |
| EDISON | Coding Agent | Code, tests, refactor, app development | Dedicated profile | High |
| PYTHAGORAS | Research Agent | Research, comparison, analysis | Dedicated profile | Medium |
| ATLAS | Security Advisor | Threat modeling, security review | Dedicated profile | High |
| YORK | QA / Reviewer | Review, QA, testing, validation | Dedicated profile | High |
| LILITH | DevOps Agent | CI/CD, deployment, implementation | Dedicated profile | High |
| BONNEY | Documentation Agent | Technical docs, user docs, SOPs | Light or dedicated profile | Medium |
| STUSSY | Monitoring / Ops Agent | Monitoring, operations, incidents | Dedicated profile | High |

---

## 6. Agent Responsibilities

### 6.1 KUMACHII — Personal Assistant

Purpose:

```text
Primary user-facing assistant for personal and productivity workflows.
```

Responsibilities:

- receive all general requests
- classify request type
- handle simple personal work directly
- draft messages, content, notes, schedules, study plans, finance summaries
- prepare structured task brief for STELLA
- return final result to user in clear language

Can handle directly:

- office task summary
- content draft
- simple campus learning plan
- personal finance organization
- email or message draft
- simple document formatting

Must delegate when:

- task has multiple phases
- project planning is needed
- coding is needed
- architecture is needed
- security review is needed
- deployment is needed
- QA is needed

---

### 6.2 STELLA — Orchestrator / Main Agent

Purpose:

```text
Workflow brain and project manager.
```

Responsibilities:

- classify task
- determine workflow phase
- select agents
- create task packets
- delegate work
- track state
- enforce quality gates
- prevent phase skipping
- request human approval
- summarize final result

Must not:

- bypass gates
- approve own risky decision
- deploy production
- silently change scope
- allow implementation before required planning

Outputs:

- task plan
- task status
- decision log
- delegation packets
- final summary

---

### 6.3 SHAKA — Architect Agent

Purpose:

```text
System and architecture design.
```

Responsibilities:

- create architecture document
- define system boundaries
- define components
- define data flow
- define API contracts
- define storage model
- create ADRs
- validate implementation readiness

Outputs:

```text
docs/architecture.md
docs/ADRs/*.md
docs/readiness-report.md
```

Must not:

- implement code directly unless explicitly assigned
- ignore security model
- approve deployment

---

### 6.4 EDISON — Coding Agent

Purpose:

```text
Implementation, tests, and refactoring.
```

Responsibilities:

- implement stories
- write tests first
- run tests
- fix failures
- refactor carefully
- follow architecture
- create atomic commits

Rules:

- use RED → GREEN → REFACTOR
- no hardcoded secrets
- no unapproved dependencies
- no unrelated file changes
- no architecture bypass

Outputs:

- source code
- tests
- implementation summary
- risk notes

---

### 6.5 PYTHAGORAS — Research Agent

Purpose:

```text
Research, comparison, and evidence-based analysis.
```

Responsibilities:

- gather references
- compare tools and frameworks
- analyze tradeoffs
- support architecture decisions
- summarize findings
- state uncertainty clearly

Must not:

- modify code
- make final architecture decisions
- invent unsupported claims
- approve release

Outputs:

```text
docs/research/*.md
docs/comparisons/*.md
```

---

### 6.6 ATLAS — Security Advisor Agent

Purpose:

```text
Security review and risk control.
```

Responsibilities:

- threat modeling
- credential review
- access control review
- dependency risk review
- infrastructure risk review
- deployment risk review
- mitigation planning

Can block release when:

- critical vulnerability exists
- secrets are exposed
- access control is weak
- deployment is unsafe

Outputs:

```text
docs/security/threat-model.md
docs/security/risk-matrix.md
docs/security/security-review.md
```

---

### 6.7 YORK — QA / Reviewer Agent

Purpose:

```text
Independent validation and quality assurance.
```

Responsibilities:

- review code correctness
- validate tests
- check edge cases
- check acceptance criteria
- check architecture compliance
- check maintainability
- create review report

Rules:

- must review independently
- must not approve own implementation
- must validate against PRD and architecture

Outputs:

```text
docs/qa/test-report.md
docs/reviews/code-review.md
```

---

### 6.8 LILITH — DevOps Agent

Purpose:

```text
Deployment, automation, infrastructure, and rollback.
```

Responsibilities:

- create CI/CD pipeline
- validate environment
- prepare deployment plan
- deploy staging
- run smoke tests
- prepare rollback
- document commissioning

Must not:

- deploy production without approval
- ignore failed smoke tests
- expose secrets
- skip rollback plan

Outputs:

```text
docs/deployment.md
docs/runbook.md
docs/commissioning-report.md
```

---

### 6.9 BONNEY — Documentation Agent

Purpose:

```text
Documentation and knowledge packaging.
```

Responsibilities:

- write README
- write user guide
- write technical docs
- update API docs
- update runbook
- convert agent outputs into clean documentation

Outputs:

```text
README.md
docs/user-guide.md
docs/admin-guide.md
docs/api.md
docs/runbook.md
```

---

### 6.10 STUSSY — Monitoring / Operations Agent

Purpose:

```text
Runtime monitoring, incident tracking, and operations support.
```

Responsibilities:

- monitor gateways
- monitor logs
- monitor service health
- monitor resource usage
- detect token/provider failures
- create incident reports
- recommend rollback when needed

Outputs:

```text
docs/ops/health-report.md
docs/ops/incident-report.md
docs/ops/monitoring-report.md
```

---

## 7. Workflow Lifecycle

### Phase 0 — Intake and Discovery

Owner:

```text
KUMACHII + STELLA
```

Support:

```text
PYTHAGORAS if research is needed
BONNEY if documentation is needed
```

Activities:

1. Capture user request.
2. Clarify intent.
3. Define scope.
4. Define out-of-scope.
5. Identify assumptions.
6. Define expected output.
7. Define success criteria.

Outputs:

```text
docs/intake/task-brief.md
CONTEXT.md
```

Gate 0:

```markdown
- [ ] Problem is clear
- [ ] Goal is clear
- [ ] Scope is bounded
- [ ] Out-of-scope is listed
- [ ] Assumptions are listed
- [ ] Success criteria are defined
Decision: PASS / CONCERNS / FAIL
```

---

### Phase 1 — Planning

Owner:

```text
STELLA
```

Support:

```text
PYTHAGORAS
BONNEY
```

Activities:

1. Create PRD.
2. Define user stories.
3. Define acceptance criteria.
4. Define non-functional requirements.
5. Define constraints.
6. Define success metrics.

Outputs:

```text
docs/PRD.md
docs/epics/*.md
```

Gate 1:

```markdown
- [ ] PRD exists
- [ ] Stories exist
- [ ] Acceptance criteria exist
- [ ] NFRs exist
- [ ] Success metrics are measurable
- [ ] Scope is stable
Decision: PASS / CONCERNS / FAIL
```

---

### Phase 2 — Architecture / Solutioning

Owner:

```text
SHAKA
```

Support:

```text
STELLA
PYTHAGORAS
ATLAS
```

Activities:

1. Create architecture.
2. Define components.
3. Define data flow.
4. Define API contracts.
5. Define storage model.
6. Define security model.
7. Define deployment model.
8. Create ADRs.
9. Run readiness check.

Outputs:

```text
docs/architecture.md
docs/ADRs/*.md
docs/security/security-model.md
docs/readiness-report.md
```

Gate 2:

```markdown
- [ ] Architecture exists
- [ ] ADRs exist
- [ ] Components are clear
- [ ] Boundaries are clear
- [ ] Security model exists
- [ ] Deployment model exists
- [ ] Readiness is PASS
Decision: PASS / CONCERNS / FAIL
```

---

### Phase 3 — Implementation

Owner:

```text
EDISON
```

Support:

```text
SHAKA for architecture clarification
PYTHAGORAS for research
ATLAS for security-sensitive implementation
```

Per-story workflow:

```text
1. Read story.
2. Read related architecture.
3. Write failing test.
4. Implement minimum code.
5. Run test.
6. Fix failure.
7. Refactor only if needed.
8. Run relevant test suite.
9. Update docs if needed.
10. Produce implementation summary.
```

Outputs:

```text
source code
tests
implementation summary
risk notes
```

Gate 3:

```markdown
- [ ] Story complete
- [ ] Tests pass
- [ ] No hardcoded secrets
- [ ] No unauthorized dependency
- [ ] Architecture boundaries respected
- [ ] Docs updated if needed
Decision: PASS / CONCERNS / FAIL
```

---

### Phase 4 — Review / QA / Security

Owner:

```text
YORK + ATLAS
```

Support:

```text
SHAKA
BONNEY
STELLA
```

Activities:

1. Code review.
2. QA validation.
3. Security review.
4. Architecture compliance check.
5. Documentation check.
6. Fix issues.

Outputs:

```text
docs/reviews/code-review.md
docs/security/security-review.md
docs/qa/test-report.md
```

Gate 4:

```markdown
- [ ] YORK review passed
- [ ] ATLAS review passed if applicable
- [ ] Critical issues fixed
- [ ] Regression tests pass
- [ ] Documentation updated
- [ ] Human QA completed
Decision: PASS / CONCERNS / FAIL
```

---

### Phase 5 — Deploy and Operate

Owner:

```text
LILITH
```

Support:

```text
STUSSY
ATLAS
YORK
BONNEY
```

Activities:

1. Build artifact.
2. Validate environment.
3. Deploy staging.
4. Run smoke tests.
5. Request production approval.
6. Deploy production.
7. Monitor logs.
8. Prepare rollback.

Outputs:

```text
docs/deployment.md
docs/commissioning-report.md
docs/runbook.md
docs/ops/health-report.md
```

Gate 5:

```markdown
- [ ] Build passed
- [ ] Staging smoke test passed
- [ ] Rollback path exists
- [ ] Monitoring active
- [ ] Production approval exists
- [ ] Production smoke test passed
Decision: PASS / CONCERNS / FAIL
```

---

## 8. Delegation Matrix

| Request Type | Primary Agent | Supporting Agents | Reviewer |
|---|---|---|---|
| Personal task | KUMACHII | BONNEY if needed | STELLA if complex |
| Office work | KUMACHII | BONNEY, PYTHAGORAS | STELLA |
| Content creation | KUMACHII | BONNEY, PYTHAGORAS | YORK |
| Campus education | KUMACHII | PYTHAGORAS, BONNEY | STELLA |
| Finance organization | KUMACHII | PYTHAGORAS | STELLA |
| Project planning | STELLA | PYTHAGORAS, BONNEY | YORK |
| Architecture | SHAKA | PYTHAGORAS, ATLAS | YORK |
| Coding | EDISON | SHAKA, PYTHAGORAS | YORK |
| Research | PYTHAGORAS | BONNEY | STELLA |
| Security | ATLAS | SHAKA, LILITH | YORK |
| QA / testing | YORK | EDISON, ATLAS | STELLA |
| Deployment | LILITH | ATLAS, STUSSY | YORK |
| Documentation | BONNEY | SHAKA, LILITH | YORK |
| Monitoring / Ops | STUSSY | LILITH, ATLAS | STELLA |

---

## 9. Agent Handoff Packet

Use this format for every delegated task.

```yaml
task_id: TASK-0001
requested_by: KUMACHII
assigned_by: STELLA
assigned_to: EDISON
phase: implementation
priority: medium
objective: "Implement login API with tests"
context:
  - docs/PRD.md
  - docs/architecture.md
  - docs/epics/epic-auth.md
constraints:
  - no new dependency without approval
  - follow architecture boundaries
  - write tests first
expected_output:
  - code changes
  - passing tests
  - implementation summary
  - risk notes
acceptance_criteria:
  - valid login succeeds
  - invalid login fails safely
  - tests cover success and failure paths
handoff_to:
  - YORK
  - ATLAS
```

---

## 10. Runtime Deployment Model

### 10.1 Recommended Hermes Profiles

```text
~/.hermes/
├── auth.json
├── config.yaml
├── .env
├── hermes-agent/
├── profiles/
│   ├── kumachii/
│   ├── stella/
│   ├── shaka/
│   ├── edison/
│   ├── pythagoras/
│   ├── atlas/
│   ├── york/
│   ├── lilith/
│   ├── bonney/
│   └── stussy/
└── cron/
```

Each profile must contain:

```text
auth.json
config.yaml
.env
memory/
sessions/
skills/
```

### 10.2 Profile Mapping

| Profile | Agent | Purpose | Platform |
|---|---|---|---|
| kumachii | KUMACHII | Personal assistant | Telegram + Discord |
| stella | STELLA | Orchestrator | Discord |
| shaka | SHAKA | Architecture | Discord |
| edison | EDISON | Coding | Discord |
| pythagoras | PYTHAGORAS | Research | Discord |
| atlas | ATLAS | Security | Discord |
| york | YORK | QA / review | Discord |
| lilith | LILITH | DevOps | Discord |
| bonney | BONNEY | Documentation | Discord or light agent |
| stussy | STUSSY | Monitoring / ops | Discord |

### 10.3 Isolation Rules

- Each profile has isolated credentials.
- Each profile has isolated memory.
- Each profile has isolated sessions.
- Each profile has isolated skills.
- Credentials are copied intentionally.
- No profile assumes access to global credentials.

---

## 11. Platform Rules

### 11.1 Token Rule

```text
One bot token can be used by one gateway only.
```

Do not reuse the same Telegram or Discord token across profiles.

### 11.2 Telegram Rule

Use Telegram only for KUMACHII unless there is a clear reason.

Dedicated agents should normally use Discord only.

### 11.3 Environment Variable Rule

Do not set unused token variables to empty strings.

Use comments:

```bash
# TELEGRAM_BOT_TOKEN=
# TELEGRAM_ALLOWED_USERS=
# TELEGRAM_HOME_CHANNEL=
```

### 11.4 Provider Switching Rule

When switching provider:

```bash
<profile> config set model.base_url ""
<profile> config set model.api_key ""
systemctl --user restart hermes-gateway-<profile>.service
```

---

## 12. Systemd Service Template

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

Recommended service names:

```text
hermes-gateway-kumachii.service
hermes-gateway-stella.service
hermes-gateway-shaka.service
hermes-gateway-edison.service
hermes-gateway-pythagoras.service
hermes-gateway-atlas.service
hermes-gateway-york.service
hermes-gateway-lilith.service
hermes-gateway-bonney.service
hermes-gateway-stussy.service
```

---

## 13. Standard Commands

### 13.1 Profile Check

```bash
hermes profile list
```

### 13.2 Gateway Status

```bash
<profile> gateway status
systemctl --user status hermes-gateway-<profile>.service
```

### 13.3 Gateway Logs

```bash
journalctl --user -u hermes-gateway-<profile>.service -f
journalctl --user -u hermes-gateway-<profile>.service --since "1 hour ago" --no-pager
```

### 13.4 Restart Gateway

```bash
systemctl --user restart hermes-gateway-<profile>.service
```

### 13.5 Test Chat

```bash
<profile> chat -q "ping"
```

### 13.6 Verify Credentials

```bash
cat ~/.hermes/profiles/<profile>/auth.json | python3 -c "import sys,json; d=json.load(sys.stdin); print(list(d.get('credential_pool',{}).keys()))"
```

---

## 14. Minimal Deployment Plan

```text
1. Install Hermes runtime.
2. Create KUMACHII/default profile.
3. Configure KUMACHII model and platform.
4. Create STELLA profile.
5. Create specialist profiles.
6. Disable Telegram on dedicated profiles.
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

---

## 15. Commissioning Checklist

### 15.1 Infrastructure

```markdown
- [ ] Server accessible
- [ ] SSH working
- [ ] Python 3.11+ available
- [ ] systemd user service available
- [ ] network to provider available
- [ ] enough RAM available
```

### 15.2 Runtime

```markdown
- [ ] Hermes installed
- [ ] virtual environment available
- [ ] CLI works
- [ ] profiles created
- [ ] profile configs exist
- [ ] profile auth files exist
- [ ] profile env files exist
```

### 15.3 Platform

```markdown
- [ ] Telegram token used only by KUMACHII
- [ ] Discord token unique per profile
- [ ] channel IDs configured
- [ ] bot permissions validated
```

### 15.4 Service

```markdown
- [ ] systemd services installed
- [ ] services enabled
- [ ] services started
- [ ] restart policy active
- [ ] logs clean
```

---

## 16. Testing Checklist

### 16.1 Gateway Test

```bash
systemctl --user status hermes-gateway-kumachii.service
systemctl --user status hermes-gateway-stella.service
systemctl --user status hermes-gateway-shaka.service
systemctl --user status hermes-gateway-edison.service
systemctl --user status hermes-gateway-pythagoras.service
systemctl --user status hermes-gateway-atlas.service
systemctl --user status hermes-gateway-york.service
systemctl --user status hermes-gateway-lilith.service
systemctl --user status hermes-gateway-bonney.service
systemctl --user status hermes-gateway-stussy.service
```

Expected:

```text
Active: active (running)
```

### 16.2 Chat Test

```bash
kumachii chat -q "ping - respond with KUMACHII online"
stella chat -q "ping - respond with STELLA online"
shaka chat -q "ping - respond with SHAKA online"
edison chat -q "ping - respond with EDISON online"
pythagoras chat -q "ping - respond with PYTHAGORAS online"
atlas chat -q "ping - respond with ATLAS online"
york chat -q "ping - respond with YORK online"
lilith chat -q "ping - respond with LILITH online"
bonney chat -q "ping - respond with BONNEY online"
stussy chat -q "ping - respond with STUSSY online"
```

### 16.3 Credential Isolation Test

```bash
for profile in kumachii stella shaka edison pythagoras atlas york lilith bonney stussy; do
  echo "=== $profile ==="
  cat ~/.hermes/profiles/$profile/auth.json | python3 -c "import sys,json; d=json.load(sys.stdin); print(list(d.get('credential_pool',{}).keys()))"
done
```

### 16.4 Token Conflict Test

```bash
journalctl --user --since "10 minutes ago" | grep -i "token already in use"
```

Expected:

```text
No output
```

### 16.5 End-to-End Delegation Test

Test prompt:

```text
KUMACHII, ask STELLA to plan a simple personal finance dashboard. STELLA must delegate research to PYTHAGORAS, architecture to SHAKA, security review to ATLAS, QA checklist to YORK, and documentation to BONNEY.
```

Expected:

```text
- KUMACHII creates intake.
- STELLA creates task plan.
- PYTHAGORAS returns research summary.
- SHAKA returns architecture draft.
- ATLAS returns security notes.
- YORK returns QA checklist.
- BONNEY returns documentation outline.
- STELLA summarizes final workflow.
```

---

## 17. Tool Access Model

| Agent | Read Files | Write Files | Execute Commands | Research | Deploy | Security Scan |
|---|---:|---:|---:|---:|---:|---:|
| KUMACHII | Limited | Limited | No | Yes | No | No |
| STELLA | Yes | Planning docs | Limited | Yes | No | No |
| SHAKA | Yes | Architecture docs | Limited | Yes | No | Limited |
| EDISON | Yes | Code/tests | Yes | Limited | No | Limited |
| PYTHAGORAS | Yes | Research docs | No | Yes | No | No |
| ATLAS | Yes | Security docs | Limited | Yes | No | Yes |
| YORK | Yes | Review docs | Test only | Limited | No | Limited |
| LILITH | Yes | Deploy docs/config | Yes | Limited | With approval | Yes |
| BONNEY | Yes | Docs | No | Limited | No | No |
| STUSSY | Logs/metrics | Ops reports | Monitoring commands | No | No | Limited |

---

## 18. Memory Model

### 18.1 Memory Types

```text
Short-term memory:
- active task
- temporary context
- current decision

Long-term memory:
- user preferences
- project conventions
- architecture decisions
- recurring workflows

Agent-local memory:
- role-specific patterns
- previous sessions
- specialist knowledge

Shared knowledge base:
- approved documents
- ADRs
- PRDs
- runbooks
- research notes
```

### 18.2 Memory Rules

- KUMACHII stores personal preferences.
- STELLA stores workflow and project state.
- SHAKA stores architecture decisions.
- EDISON stores implementation patterns.
- PYTHAGORAS stores research notes.
- ATLAS stores security policies.
- YORK stores QA checklists.
- LILITH stores deployment references.
- BONNEY stores documentation standards.
- STUSSY stores operational baselines.

Do not auto-share sensitive memory across agents.

Use handoff documents.

---

## 19. Token Optimization

### 19.1 Strategy

Use:

- focused task scope
- context reset between phases
- separate review sessions
- command output compression
- code context reduction
- concise status reports

### 19.2 Recommended Tools

| Tool | Purpose | Best Use |
|---|---|---|
| RTK | Command output compression | git, docker, kubectl, test logs |
| Caveman | Response compression | long-running agent sessions |
| code-review-graph | Code context and blast radius | large codebase review |
| LSP | Deterministic code navigation | refactor, symbol lookup |
| AST-grep | Structural search and transform | safe code edits |

### 19.3 Reset Context When

- phase changes
- review starts
- agent becomes repetitive
- assumptions become unclear
- context becomes too large
- output quality drops

---

## 20. Security Model

### 20.1 Isolation

Each dedicated agent must have:

- isolated profile
- isolated credentials
- isolated memory
- isolated sessions
- isolated skills
- dedicated gateway service

### 20.2 Credential Handling

Rules:

- never commit credentials
- never place raw secrets in prompts
- use `.env` or secret manager
- copy credentials intentionally
- rotate leaked credentials
- keep least privilege per profile

### 20.3 Platform Access

Rules:

- KUMACHII may use Telegram and Discord
- dedicated agents should use Discord only by default
- one token per bot
- one bot per profile
- restrict channels per bot

### 20.4 Tool Access

Rules:

- PYTHAGORAS is read-only by default
- ATLAS can block release
- YORK can block quality gate
- EDISON can modify code only inside assigned scope
- LILITH can deploy only after approval
- STUSSY monitors and reports, not deploys

---

## 21. Troubleshooting

### 21.1 Gateway Will Not Start

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

### 21.2 Token Already in Use

Cause:

```text
same bot token used by multiple gateways
```

Fix:

```text
1. Stop conflicting gateway.
2. Assign unique token.
3. Disable unused platform.
4. Restart affected service.
```

### 21.3 Chat Timeout

Check:

```bash
<profile> gateway health
<profile> config get model.provider
<profile> config get model.default
cat ~/.hermes/profiles/<profile>/auth.json
```

### 21.4 Wrong Model Used

Fix:

```bash
<profile> config set model.base_url ""
<profile> config set model.api_key ""
systemctl --user restart hermes-gateway-<profile>.service
```

### 21.5 Credential Not Found

Check:

```bash
cat ~/.hermes/profiles/<profile>/auth.json | python3 -m json.tool
```

Fix:

```text
1. Copy required provider credential into profile auth.json.
2. Restart gateway.
3. Test chat.
```

---

## 22. Rollback

### 22.1 Stop One Agent

```bash
systemctl --user stop hermes-gateway-<profile>.service
systemctl --user disable hermes-gateway-<profile>.service
```

### 22.2 Restart One Agent

```bash
systemctl --user restart hermes-gateway-<profile>.service
```

### 22.3 Full Rollback to KUMACHII Only

```bash
systemctl --user stop hermes-gateway-stella.service
systemctl --user stop hermes-gateway-shaka.service
systemctl --user stop hermes-gateway-edison.service
systemctl --user stop hermes-gateway-pythagoras.service
systemctl --user stop hermes-gateway-atlas.service
systemctl --user stop hermes-gateway-york.service
systemctl --user stop hermes-gateway-lilith.service
systemctl --user stop hermes-gateway-bonney.service
systemctl --user stop hermes-gateway-stussy.service

systemctl --user restart hermes-gateway-kumachii.service
```

---

## 23. Required Project File Structure

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
│   ├── security-review.md
│   └── deployment-check.md
├── docs/
│   ├── intake/
│   │   └── task-brief.md
│   ├── PRD.md
│   ├── architecture.md
│   ├── deployment.md
│   ├── runbook.md
│   ├── ADRs/
│   │   └── 000-template.md
│   ├── epics/
│   │   └── epic-000-template.md
│   ├── research/
│   │   └── research-template.md
│   ├── security/
│   │   ├── threat-model.md
│   │   └── security-review.md
│   ├── qa/
│   │   └── test-report.md
│   ├── reviews/
│   │   └── review-template.md
│   └── ops/
│       ├── health-report.md
│       └── incident-report.md
├── src/
├── tests/
├── scripts/
└── .github/
```

---

## 24. AGENTS.md Template

Copy this into project `AGENTS.md`.

```markdown
# KUMACHII-STELLA Agent Operating Guide

## Identity

You are part of a structured multi-agent workflow.

Agents:

- KUMACHII: personal assistant and user interface
- STELLA: orchestrator and workflow owner
- SHAKA: architect
- EDISON: coding agent
- PYTHAGORAS: research agent
- ATLAS: security advisor
- YORK: QA and reviewer
- LILITH: DevOps and implementation
- BONNEY: documentation
- STUSSY: monitoring and operations

## Priorities

1. Alignment
2. Correctness
3. Security
4. Simplicity
5. Testability
6. Maintainability
7. Clear documentation

## Core Rules

- Do not execute vague requests.
- Do not skip phases.
- Do not bypass quality gates.
- Do not add dependencies without approval.
- Do not hardcode secrets.
- Do not modify unrelated files.
- Do not deploy production without approval.
- Do not invent unsupported claims.
- Keep context small.
- Use documents for handoff.

## Phase Rules

| Condition | Phase | Required Action |
|---|---|---|
| No clear task brief | Discovery | clarify intent and scope |
| No PRD | Planning | create PRD |
| No architecture | Solutioning | create architecture |
| Stories approved | Implementation | implement with TDD |
| Code complete | Review | run QA and security review |
| Review passed | Deploy | deploy with approval |
| Deployment complete | Operate | monitor and report |

## Handoff Rules

- KUMACHII receives user request.
- STELLA classifies and delegates.
- PYTHAGORAS researches.
- SHAKA designs.
- EDISON implements.
- YORK reviews.
- ATLAS validates security.
- BONNEY documents.
- LILITH deploys.
- STUSSY monitors.

## Coding Rules

- Write tests first.
- Make small changes.
- Verify after each change.
- Keep code runnable.
- Avoid premature abstraction.
- Do not add dependencies without approval.
- Do not modify unrelated files.
- Do not hardcode secrets.
- Do not bypass architecture boundaries.

## Review Rules

Check:

- correctness
- edge cases
- test coverage
- security
- architecture compliance
- documentation impact
- unnecessary complexity

## Escalation Rules

Stop and escalate when:

- requirement is unclear
- scope changes
- dependency is needed
- architecture does not cover the case
- security risk appears
- test fails repeatedly
- deployment can affect production

## Output Rules

- Use compact sentences.
- Be direct.
- Do not bluff.
- State uncertainty clearly.
- Show commands when useful.
- Show file paths when relevant.
- Summarize decisions and next actions.
```

---

## 25. Definition of Done

### 25.1 Task Done

A task is done only when:

- requirement is satisfied
- expected output is delivered
- tests pass if relevant
- review passed if relevant
- security checked if relevant
- docs updated if relevant
- deployment verified if relevant
- user-facing summary is provided

### 25.2 Project Done

A project is done only when:

- all planned stories are complete
- all gates pass
- deployment works
- rollback exists
- runbook exists
- monitoring is active
- human owner accepts result

---

## 26. Operating Rules Summary

```text
KUMACHII is the user interface.
STELLA is the workflow brain.
SHAKA designs.
EDISON builds.
PYTHAGORAS researches.
ATLAS secures.
YORK validates.
LILITH deploys.
BONNEY documents.
STUSSY monitors.

Documents are contracts.
No phase skipping.
No implementation before required planning.
No production deployment without approval.
No security bypass.
No release without review.
Keep context small.
Reset context between major phases.
Use token optimization for large work.
Human judgment remains final.
```

---

## 27. Example End-to-End Flow

User request:

```text
KUMACHII, help me build a personal finance dashboard app.
```

Workflow:

```text
1. KUMACHII captures request.
2. KUMACHII creates intake brief.
3. STELLA classifies as software project.
4. PYTHAGORAS researches finance dashboard patterns.
5. STELLA creates PRD.
6. SHAKA creates architecture.
7. ATLAS reviews privacy and security model.
8. EDISON implements stories with TDD.
9. YORK reviews code and tests.
10. BONNEY writes README and user guide.
11. LILITH deploys staging.
12. STUSSY monitors health.
13. STELLA summarizes final status.
14. KUMACHII explains result to user.
```

---

## 28. Final Notes

Keep the system strict where risk is high.

Keep it lightweight where risk is low.

Use dedicated agents for isolation.

Use light agents only for simple routing.

Use documents for handoff.

Use gates for control.

Use humans for final judgment.
