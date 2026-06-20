# KUMACHII-STELLA Multi-Agent Workflow Framework

**Version:** 1.1.0  
**Status:** Final Merged Reference Architecture  
**Format:** Markdown  
**Naming Format:** lowercase kebab-case with semantic version  
**Purpose:** Deployment-ready operating documentation for a personal, project, software, research, security, QA, DevOps, documentation, RAG, monitoring, and operations multi-agent workflow.  
**Style:** Compact, direct, deployment-adaptable, no bluffing.

---

## 1. Core Objective

Build a multi-agent system that is:

- structured
- role-specific
- auditable
- token-efficient
- secure by design
- isolated by runtime profile
- controlled by quality gates
- adaptable for personal and project workflows
- practical for software, infrastructure, research, security, QA, DevOps, documentation, RAG, monitoring, and operations

Core separation:

```text
KUMACHII = personal assistant and user-facing intake
STELLA   = orchestrator, workflow state owner, and project controller
Specialist agents = execution, validation, security, documentation, deployment, and operations
```

KUMACHII and STELLA are not merged.

Use this rule:

```text
Personal/simple work → KUMACHII handles directly.
Project/multi-phase work → KUMACHII forwards structured work to STELLA.
STELLA controls delegation, gates, state, and final synthesis.
```

---

## 2. Framework Foundation

This framework combines six layers:

1. **SOP Discipline** — alignment first, Smart Zone, vertical slicing, TDD, human review.
2. **BMAD Structure** — discovery, planning, solutioning, implementation, review, deploy, operate.
3. **OMO-Style Orchestration** — orchestrator, specialist agents, read-only consultation, background research.
4. **Hermes Runtime Isolation** — dedicated profiles, isolated credentials, isolated memory, isolated sessions.
5. **Token Optimization** — context reset, focused scope, RTK, Caveman, code-review-graph, LSP, AST-grep, RAG retrieval.
6. **Operational Control** — monitoring, runbooks, rollback, incident response, periodic review.

---

## 3. Design Principles

### 3.1 Alignment Before Execution

Do not execute unclear work.

Every major task must define:

- intent
- scope
- out-of-scope
- constraints
- assumptions
- expected output
- success criteria
- owner agent
- reviewer agent

### 3.2 Smart Zone Discipline

Agents perform better with focused context.

Rules:

- split large tasks into small tasks
- use vertical slices
- use phase-based sessions
- reset context between major phases
- use separate sessions for review
- do not load the whole codebase unless required
- use RAG retrieval instead of dumping documents
- use code-review-graph for large codebases
- summarize before handoff

### 3.3 Documents as Contracts

Documents are control points, not decoration.

Required documents:

- `CONTEXT.md`
- `docs/product-brief.md`
- `docs/PRD.md`
- `docs/architecture.md`
- `docs/ADRs/*.md`
- `docs/epics/*.md`
- `docs/risks.md`
- `docs/reviews/*.md`
- `docs/security/*.md`
- `docs/deployment.md`
- `docs/runbook.md`
- `docs/operations.md`
- `docs/rag/*.md`

### 3.4 Vertical Slicing

Avoid horizontal delivery:

```text
Database first → API later → UI last
```

Use vertical delivery:

```text
One story = data + logic + interface + test + documentation impact
```

Each slice must be independently testable.

### 3.5 Test-Driven Implementation

Default implementation cycle:

```text
RED → GREEN → REFACTOR → VERIFY → REVIEW
```

Rules:

- write failing test first
- implement minimal solution
- verify result
- refactor only when needed
- never delete failing tests to pass
- never skip critical tests silently

### 3.6 Human-in-the-Loop

Human approval is mandatory for:

- scope changes
- architecture changes
- dependency additions
- security exceptions
- credential changes
- production deployment
- rollback decision
- destructive operations
- final acceptance

### 3.7 Security First and Least Privilege

Default behavior:

- least privilege
- isolated credentials
- no shared bot tokens
- no hardcoded secrets
- no production deployment without approval
- no risky remediation without approval
- security review before release

---

## 4. Reference Architecture

```text
┌─────────────────────────────────────────────────────────────────────┐
│                          USER / OPERATOR                            │
│  Personal tasks, office work, campus, finance, projects, software   │
└──────────────────────────────┬──────────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────────────┐
│ LAYER 1 — USER-FACING INTAKE                                        │
│ KUMACHII                                                            │
│ - personal assistant                                                │
│ - general intake and triage                                         │
│ - simple personal task execution                                    │
│ - converts complex requests into structured briefs for STELLA       │
└──────────────────────────────┬──────────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────────────┐
│ LAYER 2 — ORCHESTRATION                                             │
│ STELLA                                                              │
│ - project manager                                                   │
│ - workflow state owner                                              │
│ - delegation controller                                             │
│ - quality gate enforcer                                             │
│ - decision log owner                                                │
│ - human approval checkpoint                                         │
└─────────────┬───────────────┬───────────────┬──────────────────────┘
              │               │               │
              ▼               ▼               ▼
┌────────────────────┐ ┌────────────────────┐ ┌──────────────────────┐
│ BUILD LAYER         │ │ CONTROL LAYER       │ │ OPERATE LAYER          │
│ SHAKA   Architect   │ │ ATLAS   Security    │ │ LILITH  DevOps         │
│ EDISON  Coding      │ │ YORK    QA/Review   │ │ STUSSY  Monitoring/Ops │
│ PYTHAGORAS Research │ │ BONNEY  Docs/RAG    │ │ Runtime Tools          │
└────────────────────┘ └────────────────────┘ └──────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────────────┐
│ KNOWLEDGE, TOOL, AND RUNTIME LAYER                                  │
│ Project docs | RAG store | Source repos | Issue tracker | CI/CD      │
│ Secrets manager | Observability stack | RTK | Caveman | CRG | LSP    │
│ AST-grep | MCP tools | Hermes isolated profiles                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 5. Agent Roster

| Agent | Role | Main Purpose | Access Level | Recommended Runtime |
|---|---|---|---|---|
| **KUMACHII** | Personal Assistant | Office work, content, education, finance, personal productivity, intake | Medium | Light or dedicated profile |
| **STELLA** | Orchestrator / Main Agent | Project management, workflow state, delegation, quality gates | High | Dedicated profile |
| **SHAKA** | Architect Agent | System design, architecture, ADRs, technical decisions | High | Dedicated profile |
| **EDISON** | Coding Agent | Code, app development, tests, refactoring | High | Dedicated profile |
| **PYTHAGORAS** | Research Agent | Research, comparison, evidence collection, source validation | Read-only | Dedicated profile |
| **ATLAS** | Security Advisor Agent | Threat modeling, security review, risk mitigation | High review authority | Dedicated profile |
| **YORK** | QA / Reviewer Agent | Review, QA, testing, verification, acceptance validation | Review-only + test execution | Dedicated profile |
| **LILITH** | DevOps Agent | CI/CD, deployment, infrastructure, commissioning, rollback | High but gated | Dedicated profile |
| **BONNEY** | Documentation & RAG Knowledge Agent | Documentation, knowledge base, RAG ingestion, retrieval quality | Docs + KB write | Dedicated profile |
| **STUSSY** | Monitoring & Operations Agent | Monitoring, operations, incident triage, runbooks, health checks | Ops read + gated action | Dedicated profile |

---

## 6. Deployment Modes

### 6.1 Light Agent Mode

Use for low-risk, low-isolation tasks.

Best for:

- simple personal assistant tasks
- content drafting
- education support
- simple finance organization
- lightweight document formatting
- channel-based routing

Recommended light agents:

```text
KUMACHII subdomains or helper prompts:
- OfficeOps
- ContentOps
- EduOps
- FinanceOps
- DocOps
```

### 6.2 Dedicated Profile Mode

Use for agents that need:

- isolated credentials
- isolated memory
- dedicated model
- separate sessions
- auditability
- role-specific tool access
- risk separation

Recommended dedicated agents:

```text
STELLA
SHAKA
EDISON
PYTHAGORAS
ATLAS
YORK
LILITH
BONNEY
STUSSY
```

### 6.3 Hybrid Mode

Recommended default.

```text
User-facing:
  KUMACHII as personal assistant and intake layer

Orchestration:
  STELLA as workflow controller and project manager

Dedicated specialists:
  SHAKA      architecture
  EDISON     coding
  PYTHAGORAS research
  ATLAS      security
  YORK       QA/review
  LILITH     DevOps
  BONNEY     documentation/RAG
  STUSSY     monitoring/operations
```

---

## 7. Agent Contracts

### 7.1 KUMACHII — Personal Assistant

**Purpose:** Assist personal and productivity tasks and serve as first intake layer.

Scope:

- office work
- meeting notes
- email drafts
- content creation
- campus education
- finance organization
- personal planning
- simple document formatting

Must:

- classify request type
- keep output practical
- separate facts from assumptions
- handle simple personal work directly
- prepare structured task brief for STELLA when complex
- return final result to user clearly

Must not:

- make risky financial/legal decisions autonomously
- access private systems without explicit permission
- act as final approver for business/project delivery
- bypass STELLA for project-level work

### 7.2 STELLA — Orchestrator / Main Agent

**Purpose:** Own workflow state, task routing, and quality gates.

Responsibilities:

- classify requests
- determine workflow phase
- select agents
- create task packets
- delegate tasks
- track state
- maintain decision log
- enforce quality gates
- prevent phase skipping
- request human approval when needed
- consolidate outputs from all agents

Must:

- keep task scope bounded
- assign one owner and one reviewer per task
- require human approval for risky changes
- summarize final result

Must not:

- bypass review
- approve its own risky decisions
- deploy production
- silently expand scope
- allow implementation before required planning

### 7.3 SHAKA — Architect Agent

**Purpose:** Own system and solution architecture.

Responsibilities:

- create architecture document
- define system boundaries
- define components
- define data flow
- define API contracts
- define storage model
- define security model
- define deployment model
- create ADRs
- run implementation readiness check

Outputs:

```text
docs/architecture.md
docs/ADRs/*.md
docs/readiness-report.md
```

Must not:

- write production code as primary owner
- ignore security constraints
- approve deployment

### 7.4 EDISON — Coding Agent

**Purpose:** Implement stories with tests.

Responsibilities:

- read story and architecture
- write tests first
- implement minimal solution
- run tests
- fix failures
- refactor carefully
- create atomic commits
- document code impact

Must:

- follow TDD
- respect architecture boundaries
- make small changes
- verify after each change
- request approval for new dependencies

Must not:

- redesign architecture unilaterally
- add dependencies silently
- delete failing tests to pass
- modify unrelated files
- hardcode secrets

### 7.5 PYTHAGORAS — Research Agent

**Purpose:** Provide evidence-based research.

Responsibilities:

- gather references
- compare tools and frameworks
- analyze tradeoffs
- validate sources
- support architecture decisions
- summarize uncertainty clearly

Must:

- cite sources when used
- distinguish fact from inference
- use current sources when freshness matters
- provide practical recommendations

Must not:

- modify code
- make final architecture decisions
- invent unsupported claims
- approve release

### 7.6 ATLAS — Security Advisor Agent

**Purpose:** Identify and reduce security risk.

Responsibilities:

- threat modeling
- credential review
- access control review
- dependency risk review
- security architecture review
- infrastructure risk review
- deployment risk review
- mitigation planning

Can block release for:

- exposed secrets
- critical vulnerability
- broken access control
- unsafe deployment configuration
- missing rollback for high-risk production change

Must not:

- approve risky exceptions alone
- expose secrets in reports
- perform destructive testing without approval

### 7.7 YORK — QA / Reviewer Agent

**Purpose:** Validate correctness and quality independently.

Responsibilities:

- review code correctness
- validate tests
- check edge cases
- check acceptance criteria
- check architecture compliance
- check maintainability
- validate documentation impact
- create review report

Can block release for:

- failed acceptance criteria
- failed tests
- unreviewed critical code
- incomplete documentation for user-impacting change

Must not:

- review its own implementation as final approval
- ignore failed tests
- approve incomplete stories

### 7.8 LILITH — DevOps Agent

**Purpose:** Build, deploy, verify, and rollback.

Responsibilities:

- environment setup
- CI/CD configuration
- deployment plan
- smoke tests
- rollback procedure
- release checklist
- commissioning test

Must:

- validate environment before deployment
- keep rollback ready
- require approval for production
- document commands
- avoid exposing secrets

Must not:

- deploy production without approval
- change security settings silently
- ignore failed smoke tests

### 7.9 BONNEY — Documentation & RAG Knowledge Agent

**Purpose:** Maintain documentation and knowledge retrieval.

Responsibilities:

- write technical docs
- update README
- maintain runbooks
- manage knowledge ingestion
- chunk documents
- tag metadata
- validate retrieval quality
- prevent stale knowledge

Must:

- preserve source-of-truth hierarchy
- mark stale or uncertain content
- separate raw source from generated summary
- keep docs compact
- include metadata for RAG

Must not:

- overwrite source-of-truth without approval
- ingest secrets
- mix unsupported claims into knowledge base
- treat generated summary as primary source

### 7.10 STUSSY — Monitoring & Operations Agent

**Purpose:** Monitor system health and support operations.

Responsibilities:

- monitor services
- summarize logs
- detect anomalies
- classify incidents
- run health checks
- maintain runbooks
- recommend remediation
- track operational KPIs

Can block release for:

- missing monitoring
- missing runbook
- recurring unresolved production incident
- failed smoke test

Must not:

- perform destructive remediation without approval
- hide recurring errors
- mark incident resolved without verification

---

## 8. Workflow Lifecycle

### Phase 0 — Intake and Discovery

**Owner:** KUMACHII for personal intake, STELLA for project intake  
**Support:** PYTHAGORAS, BONNEY  
**Reviewer:** User

Goal:

- understand request
- define scope
- identify risks
- choose workflow path

Activities:

1. Capture user request.
2. Classify task type.
3. Clarify intent.
4. Define scope and out-of-scope.
5. List assumptions.
6. Identify affected systems.
7. Select owner and reviewer agents.
8. Create product brief or task brief.

Outputs:

```text
CONTEXT.md
docs/product-brief.md
docs/intake/task-brief.md
```

Gate 0:

```markdown
- [ ] Problem is clear
- [ ] Goal is clear
- [ ] Scope is bounded
- [ ] Out-of-scope is listed
- [ ] Assumptions are listed
- [ ] Key terms are defined
- [ ] Owner agent is assigned
- [ ] Reviewer agent is assigned
Decision: PASS / CONCERNS / FAIL
```

### Phase 1 — Planning

**Owner:** STELLA  
**Support:** KUMACHII, PYTHAGORAS, BONNEY  
**Reviewer:** User

Goal:

- define what must be delivered

Activities:

1. Create PRD.
2. Define user stories.
3. Define acceptance criteria.
4. Define non-functional requirements.
5. Create initial risks.
6. Prepare documentation structure.

Outputs:

```text
docs/PRD.md
docs/epics/*.md
docs/risks.md
```

Gate 1:

```markdown
- [ ] PRD exists
- [ ] Stories exist
- [ ] Acceptance criteria exist
- [ ] NFRs exist
- [ ] Out-of-scope is listed
- [ ] Success criteria are measurable
- [ ] Scope is stable
Decision: PASS / CONCERNS / FAIL
```

### Phase 2 — Solutioning and Architecture

**Owner:** SHAKA  
**Support:** STELLA, PYTHAGORAS, ATLAS, BONNEY  
**Reviewer:** STELLA, User

Goal:

- define how the solution will be built

Activities:

1. Create architecture.
2. Define components.
3. Define data flow.
4. Define API contracts.
5. Define storage model.
6. Define security model.
7. Define deployment model.
8. Create ADRs.
9. Run implementation readiness check.

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
- [ ] ADRs exist for major decisions
- [ ] Component boundaries are clear
- [ ] Data flow is clear
- [ ] API contracts are defined
- [ ] Security model is defined
- [ ] Deployment model is defined
- [ ] Implementation readiness is PASS
Decision: PASS / CONCERNS / FAIL
```

### Phase 3 — Implementation

**Owner:** EDISON  
**Support:** SHAKA, PYTHAGORAS, ATLAS, BONNEY  
**Reviewer:** YORK

Goal:

- build story by story with tests

Per-story workflow:

```text
1. Read story.
2. Read related architecture section.
3. Write failing test.
4. Implement minimum code.
5. Run test.
6. Fix failure.
7. Refactor only if needed.
8. Run relevant test suite.
9. Update docs if needed.
10. Create atomic commit.
11. Hand off to YORK.
```

Outputs:

```text
source code
tests
implementation notes
commit reference
risk notes
```

Gate 3:

```markdown
- [ ] Story complete
- [ ] Tests pass
- [ ] Acceptance criteria satisfied
- [ ] No hardcoded secrets
- [ ] No unapproved dependency
- [ ] Architecture boundaries respected
- [ ] Documentation impact handled
Decision: PASS / CONCERNS / FAIL
```

### Phase 4 — Review, QA, and Security

**Owner:** YORK  
**Support:** ATLAS, SHAKA, BONNEY  
**Reviewer:** STELLA, User

Goal:

- validate correctness, quality, security, and documentation

Activities:

1. Code review.
2. Test review.
3. Acceptance criteria review.
4. Security review.
5. Architecture compliance review.
6. Documentation review.
7. Defect logging.
8. Retest after fixes.

Outputs:

```text
docs/reviews/code-review-*.md
docs/security/security-review-*.md
docs/qa/test-report.md
defect list
acceptance decision
```

Gate 4:

```markdown
- [ ] Code review passed
- [ ] QA review passed
- [ ] Security review passed if applicable
- [ ] Critical issues fixed
- [ ] Regression tests pass
- [ ] Documentation updated
- [ ] Human QA completed where required
Decision: PASS / CONCERNS / FAIL
```

### Phase 5 — Deployment

**Owner:** LILITH  
**Support:** ATLAS, STUSSY, BONNEY  
**Reviewer:** STELLA, User

Goal:

- release safely and verify deployment

Activities:

1. Build artifact.
2. Run pre-deploy checks.
3. Validate secrets and config.
4. Deploy to staging.
5. Run smoke tests.
6. Request production approval.
7. Deploy production.
8. Run post-deploy checks.
9. Prepare rollback report.

Outputs:

```text
docs/deployment.md
docs/commissioning-report.md
docs/runbook.md
smoke test result
rollback note
```

Gate 5:

```markdown
- [ ] Build passed
- [ ] Staging deployment passed
- [ ] Smoke test passed
- [ ] Rollback path exists
- [ ] Monitoring active
- [ ] Production approval exists
- [ ] Production smoke test passed
Decision: PASS / CONCERNS / FAIL
```

### Phase 6 — Operate, Monitor, and Improve

**Owner:** STUSSY  
**Support:** LILITH, ATLAS, BONNEY  
**Reviewer:** STELLA

Goal:

- keep system healthy and improve continuously

Activities:

1. Monitor services.
2. Review logs.
3. Detect incidents.
4. Classify severity.
5. Recommend remediation.
6. Update runbooks.
7. Update RAG knowledge.
8. Create post-incident report.

Outputs:

```text
docs/operations.md
docs/runbook.md
docs/ops/health-report.md
docs/ops/incident-report.md
docs/ops/monitoring-report.md
docs/rag/freshness-report.md
```

Gate 6:

```markdown
- [ ] System health known
- [ ] Logs available
- [ ] Metrics available
- [ ] Alerts configured
- [ ] Runbook updated
- [ ] Incidents reviewed
- [ ] Recurring issues tracked
- [ ] Knowledge base updated
Decision: PASS / CONCERNS / FAIL
```

---

## 9. Delegation Matrix

| Task Type | Owner | Support | Reviewer | Approval Needed |
|---|---|---|---|---|
| Personal task | KUMACHII | BONNEY | User | User if sensitive |
| Office work | KUMACHII | BONNEY, PYTHAGORAS | STELLA if complex | If sensitive |
| Content creation | KUMACHII | BONNEY, PYTHAGORAS | YORK if public | If public/brand-impacting |
| Campus education | KUMACHII | PYTHAGORAS, BONNEY | User | No, unless submitted externally |
| Finance organization | KUMACHII | PYTHAGORAS | User | Yes for risky decisions |
| Project planning | STELLA | PYTHAGORAS, BONNEY | User | Yes |
| Architecture | SHAKA | PYTHAGORAS, ATLAS | STELLA | Yes |
| Coding | EDISON | SHAKA, BONNEY | YORK | For merge |
| Research | PYTHAGORAS | BONNEY | STELLA | If decision-impacting |
| Security review | ATLAS | SHAKA, LILITH | STELLA | Yes for exceptions |
| QA/testing | YORK | EDISON, ATLAS | STELLA | Yes for release |
| Deployment | LILITH | ATLAS, STUSSY | STELLA | Yes for production |
| Documentation | BONNEY | All agents | YORK | If source-of-truth changes |
| Monitoring/Ops | STUSSY | LILITH, ATLAS | STELLA | For remediation |

---

## 10. Handoff Protocol

Every delegated task must include:

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

Rules:

- no vague handoff
- no missing file paths
- no hidden assumptions
- no silent scope expansion
- blocked tasks return to STELLA
- risky decisions require human approval

YAML variant:

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
status: pending
```

---

## 11. Runtime Deployment Model

### 11.1 Recommended Hermes Structure

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

### 11.2 Profile Mapping

| Profile | Agent | Purpose | Platform |
|---|---|---|---|
| kumachii | KUMACHII | Personal assistant and intake | Telegram + Discord |
| stella | STELLA | Orchestrator and workflow control | Discord or default profile |
| shaka | SHAKA | Architecture | Discord |
| edison | EDISON | Coding | Discord |
| pythagoras | PYTHAGORAS | Research | Discord |
| atlas | ATLAS | Security | Discord |
| york | YORK | QA / review | Discord |
| lilith | LILITH | DevOps | Discord |
| bonney | BONNEY | Documentation and RAG | Discord or dedicated KB worker |
| stussy | STUSSY | Monitoring / operations | Discord |

### 11.3 Isolation Rules

- Each profile has isolated credentials.
- Each profile has isolated memory.
- Each profile has isolated sessions.
- Each profile has isolated skills.
- Credentials are copied intentionally.
- No profile assumes access to global credentials.
- Sensitive memory is not auto-shared across agents.
- Handoff documents are the default sharing mechanism.

---

## 12. Gateway Rules

### 12.1 Token Rule

```text
One bot token can be used by one gateway only.
```

Do not reuse Telegram or Discord tokens across profiles.

### 12.2 Telegram Rule

Use Telegram only for KUMACHII unless there is a clear reason.

Dedicated agents should normally use Discord only.

### 12.3 Environment Variable Rule

Do not set unused token variables to empty strings.

Use comments:

```bash
# TELEGRAM_BOT_TOKEN=
# TELEGRAM_ALLOWED_USERS=
# TELEGRAM_HOME_CHANNEL=
```

### 12.4 Provider Switching Rule

When switching model provider:

```bash
<profile> config set model.base_url ""
<profile> config set model.api_key ""
systemctl --user restart hermes-gateway-<profile>.service
```

This prevents stale provider configuration.

---

## 13. Systemd Service Template

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

## 14. Recommended Model Strategy

| Agent | Model Characteristics Needed |
|---|---|
| KUMACHII | balanced, multilingual, personal productivity, summarization |
| STELLA | strongest reasoning, planning, long-context coordination |
| SHAKA | architecture reasoning, diagram understanding, trade-off analysis |
| EDISON | coding, tool use, debugging, test generation |
| PYTHAGORAS | research, citations, long reading, source comparison |
| ATLAS | security reasoning, risk analysis, threat modeling |
| YORK | strict reviewer, test reasoning, edge-case detection |
| LILITH | DevOps, shell, YAML, CI/CD, Kubernetes, infrastructure reasoning |
| BONNEY | documentation, RAG, summarization, information architecture |
| STUSSY | log analysis, monitoring, incident summarization, ops reasoning |

Rules:

- do not use one model for all roles if cost and quality matter
- use cheaper fast models for simple retrieval and grep
- use strongest reasoning model for gates and architecture decisions
- use read-only strong model for independent review
- keep production-risk agents on reliable models

---

## 15. Tooling Strategy

### 15.1 Required Tools

| Tool | Purpose |
|---|---|
| Git | version control |
| Issue tracker | task and story management |
| CI/CD | automated build and test |
| Secrets manager | credential isolation |
| Test runner | quality verification |
| Markdown docs | contracts and handoff |
| RAG store | knowledge retrieval |
| Monitoring stack | operations visibility |

### 15.2 Recommended AI Tools

| Tool | Purpose | Best Use |
|---|---|---|
| RTK | command output compression | git, docker, kubectl, test logs |
| Caveman | compact agent responses | long-running sessions, repeated status reports |
| code-review-graph | code context and blast radius | large codebase review |
| LSP | deterministic code navigation | refactor, symbol lookup |
| AST-grep | structural search and transform | safe code edits |
| MCP tools | controlled external integrations | docs, repos, issue trackers |
| Vector DB | RAG retrieval | project knowledge base |

---

## 16. RAG Architecture for BONNEY

### 16.1 Knowledge Source Hierarchy

Priority order:

1. source-of-truth documents
2. architecture and ADRs
3. PRD and acceptance criteria
4. code and tests
5. deployment and runbook docs
6. research reports
7. generated summaries
8. chat history

Generated summaries must not override primary sources.

### 16.2 RAG Pipeline

```text
Ingest → Clean → Chunk → Tag → Embed → Index → Retrieve → Rerank → Answer → Cite → Validate
```

### 16.3 Metadata Required

Each chunk must include:

```yaml
source_file:
source_type:
owner_agent:
created_at:
updated_at:
project:
phase:
version:
freshness:
authority_level:
contains_secret: false
```

### 16.4 RAG Rules

- never ingest secrets
- never ingest raw credentials
- mark stale content
- cite source documents
- keep chunk size consistent
- use retrieval tests for important docs
- re-index after major documentation changes
- preserve source-of-truth hierarchy

### 16.5 Retrieval Test Template

```markdown
# RAG Retrieval Test

Question:
Expected source:
Expected answer points:
Retrieved chunks:
Pass/Fail:
Notes:
```

---

## 17. Security Model

### 17.1 Isolation

Each dedicated agent must have:

- isolated profile
- isolated credentials
- isolated memory
- isolated sessions
- isolated skills
- dedicated gateway service
- least-privilege tool access

### 17.2 Credential Rules

- never commit credentials
- never paste secrets into prompt files
- use `.env` or secret manager
- rotate leaked credentials
- copy credentials intentionally
- keep credentials role-specific
- restrict platform channels per bot

### 17.3 Tool Access Rules

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
| BONNEY | Yes | Docs/RAG | No | Limited | No | No |
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
- BONNEY stores documentation and RAG standards.
- STUSSY stores operational baselines.

Do not auto-share sensitive memory across agents.

Use handoff documents.

---

## 19. Quality Gate Templates

### Gate 0 — Discovery Approved

```markdown
# Gate 0 — Discovery Approved

- [ ] Problem is clear
- [ ] Goal is clear
- [ ] Scope is bounded
- [ ] Out-of-scope is listed
- [ ] Assumptions are listed
- [ ] Key terms are defined
- [ ] Owner and reviewer assigned

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

- [ ] Story complete
- [ ] Tests pass
- [ ] Acceptance criteria satisfied
- [ ] No hardcoded secrets
- [ ] No unapproved dependency
- [ ] Architecture boundaries respected
- [ ] Docs updated if needed

Decision: PASS / CONCERNS / FAIL
Reviewer:
Date:
Notes:
```

### Gate 4 — Review Passed

```markdown
# Gate 4 — Review Passed

- [ ] Code review passed
- [ ] QA review passed
- [ ] Security review passed
- [ ] Critical issues fixed
- [ ] Regression tests pass
- [ ] Human QA completed where required

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

### Gate 6 — Operate Ready

```markdown
# Gate 6 — Operate Ready

- [ ] Logs available
- [ ] Metrics available
- [ ] Alerts configured
- [ ] Runbook exists
- [ ] Incident path exists
- [ ] Knowledge base updated

Decision: PASS / CONCERNS / FAIL
Reviewer:
Date:
Notes:
```

---

## 20. Project File Structure

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
│   ├── qa-review.md
│   ├── deployment-check.md
│   └── rag-ingestion.md
├── docs/
│   ├── product-brief.md
│   ├── PRD.md
│   ├── architecture.md
│   ├── deployment.md
│   ├── operations.md
│   ├── runbook.md
│   ├── risks.md
│   ├── intake/
│   │   └── task-brief.md
│   ├── ADRs/
│   │   └── 000-template.md
│   ├── epics/
│   │   └── epic-000-template.md
│   ├── research/
│   │   └── research-template.md
│   ├── reviews/
│   │   └── review-000-template.md
│   ├── qa/
│   │   └── test-report.md
│   ├── security/
│   │   ├── threat-model.md
│   │   └── security-review-000-template.md
│   ├── ops/
│   │   ├── health-report.md
│   │   └── incident-report.md
│   └── rag/
│       ├── ingestion-manifest.md
│       ├── retrieval-tests.md
│       └── freshness-report.md
├── src/
├── tests/
├── scripts/
├── infra/
├── monitoring/
└── .github/
```

---

## 21. AGENTS.md Operating Guide

Copy this into project `AGENTS.md`.

```markdown
# KUMACHII-STELLA Agent Operating Guide

## Identity

You are an AI agent working inside the KUMACHII-STELLA phase-gated multi-agent workflow.

You must prioritize:

1. alignment
2. correctness
3. security
4. simplicity
5. testability
6. maintainability
7. observability

## Agent Roster

- KUMACHII: personal assistant and intake
- STELLA: orchestrator and workflow owner
- SHAKA: architect
- EDISON: coding
- PYTHAGORAS: research
- ATLAS: security
- YORK: QA/reviewer
- LILITH: DevOps
- BONNEY: documentation and RAG
- STUSSY: monitoring and operations

## Before Any Task

1. Identify current phase.
2. Check required documents.
3. Check whether scope is clear.
4. Check whether a skill applies.
5. Assign owner and reviewer.
6. Do not skip quality gates.
7. Escalate risky ambiguity to STELLA.

## Phase Rules

| Condition | Phase | Required Action |
|---|---|---|
| No clear scope | Discovery | create brief and assumptions |
| No PRD | Planning | create PRD and acceptance criteria |
| No architecture | Solutioning | create architecture and ADRs |
| Stories approved | Implementation | implement with TDD |
| Code implemented | Review | run QA and security review |
| Review passed | Deploy | deploy with approval |
| Deployed | Operate | monitor and update runbook |

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

## Research Rules

- Cite sources.
- Use current sources when freshness matters.
- Separate facts from inference.
- State uncertainty clearly.
- Do not invent unsupported claims.

## Security Rules

- Never expose secrets.
- Use least privilege.
- Validate inputs.
- Check dependency risk.
- Block critical issues.
- Do not approve security exceptions alone.

## RAG Rules

- Source-of-truth documents outrank summaries.
- Never ingest secrets.
- Mark stale knowledge.
- Include metadata.
- Validate retrieval quality.

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
- observability impact

## Escalation Rules

Stop and escalate when:

- requirement is unclear
- scope changes
- dependency is needed
- architecture does not cover the case
- security risk appears
- test fails after repeated fixes
- production may be affected
- monitoring is missing for production change

## Output Rules

- Use compact, direct sentences.
- Do not bluff.
- State uncertainty clearly.
- Show commands when useful.
- Show file paths when relevant.
- Summarize decisions and next actions.
```

---

## 22. Standard Commands

### 22.1 Profile Check

```bash
hermes profile list
```

### 22.2 Gateway Status

```bash
<profile> gateway status
systemctl --user status hermes-gateway-<profile>.service
```

### 22.3 Gateway Logs

```bash
journalctl --user -u hermes-gateway-<profile>.service -f
journalctl --user -u hermes-gateway-<profile>.service --since "1 hour ago" --no-pager
```

### 22.4 Restart Gateway

```bash
systemctl --user restart hermes-gateway-<profile>.service
```

### 22.5 Test Chat

```bash
<profile> chat -q "ping"
```

### 22.6 Verify Credentials

```bash
cat ~/.hermes/profiles/<profile>/auth.json | python3 -c "import sys,json; d=json.load(sys.stdin); print(list(d.get('credential_pool',{}).keys()))"
```

---

## 23. Commissioning Checklist

### 23.1 Infrastructure

- [ ] Server accessible
- [ ] SSH working
- [ ] Python 3.11+ available
- [ ] systemd user service available
- [ ] network to providers available
- [ ] enough RAM available
- [ ] time sync active

### 23.2 Runtime

- [ ] Hermes installed
- [ ] virtual environment available
- [ ] CLI works
- [ ] profiles created
- [ ] profile configs exist
- [ ] profile auth files exist
- [ ] profile env files exist

### 23.3 Platform

- [ ] Telegram token used only by KUMACHII
- [ ] Discord token unique per profile
- [ ] channel IDs configured
- [ ] bot permissions validated
- [ ] unused token vars commented out

### 23.4 Service

- [ ] systemd services installed
- [ ] services enabled
- [ ] services started
- [ ] restart policy active
- [ ] logs clean

---

## 24. Testing Checklist

### 24.1 Gateway Test

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

### 24.2 Chat Test

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

### 24.3 Credential Isolation Test

```bash
for profile in kumachii stella shaka edison pythagoras atlas york lilith bonney stussy; do
  echo "=== $profile ==="
  cat ~/.hermes/profiles/$profile/auth.json | python3 -c "import sys,json; d=json.load(sys.stdin); print(list(d.get('credential_pool',{}).keys()))"
done
```

### 24.4 Token Conflict Test

```bash
journalctl --user --since "10 minutes ago" | grep -i "token already in use"
```

Expected:

```text
No output
```

### 24.5 End-to-End Delegation Test

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

## 25. Operations Runbook

### 25.1 Daily Check

```bash
hermes profile list
journalctl --user --since "24 hours ago" | grep -i "error\|failed\|exception"
```

### 25.2 Health Check

```bash
for profile in kumachii stella shaka edison pythagoras atlas york lilith bonney stussy; do
  echo "=== $profile ==="
  systemctl --user is-active hermes-gateway-$profile.service
  $profile gateway health || true
done
```

### 25.3 Restart One Agent

```bash
systemctl --user restart hermes-gateway-<profile>.service
```

### 25.4 Stop One Agent

```bash
systemctl --user stop hermes-gateway-<profile>.service
systemctl --user disable hermes-gateway-<profile>.service
```

### 25.5 Full Rollback to KUMACHII Only

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

## 26. Troubleshooting

### 26.1 Gateway Will Not Start

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

### 26.2 Token Already in Use

Cause:

```text
same bot token used by multiple gateways
```

Fix:

```text
1. Stop conflicting gateway.
2. Assign unique token.
3. Disable unused platform.
4. Comment unused token vars.
5. Restart affected service.
```

### 26.3 Chat Timeout

Check:

```bash
<profile> gateway health
<profile> config get model.provider
<profile> config get model.default
cat ~/.hermes/profiles/<profile>/auth.json
```

### 26.4 Wrong Model Used

Fix:

```bash
<profile> config set model.base_url ""
<profile> config set model.api_key ""
systemctl --user restart hermes-gateway-<profile>.service
```

### 26.5 Credential Not Found

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

## 27. Token Optimization Strategy

### 27.1 RTK

Use for command output compression.

Best for:

- `git status`
- `git diff`
- `git log`
- `docker ps`
- `kubectl get`
- `npm test`
- `cargo test`

### 27.2 Caveman

Use for compact agent responses.

Best for:

- repeated status reports
- long-running coding sessions
- high-frequency agent communication

### 27.3 code-review-graph

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

### 27.4 Context Reset

Reset context when:

- phase changes
- review starts
- agent becomes repetitive
- context becomes too large
- output quality drops
- assumptions become unclear

---

## 28. Minimal Deployment Plan

```text
1. Install Hermes runtime.
2. Create KUMACHII profile.
3. Configure KUMACHII model and platform.
4. Create STELLA profile.
5. Create specialist profiles.
6. Disable Telegram on dedicated specialist profiles.
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

## 29. Example End-to-End Flow

User request:

```text
Create a secure personal finance dashboard with import, categorization, budget tracking, and monthly report export.
```

Flow:

```text
1. KUMACHII receives user request.
2. KUMACHII identifies this as project-level work.
3. KUMACHII creates intake brief.
4. STELLA accepts ownership and opens Phase 0.
5. PYTHAGORAS researches options and risks.
6. STELLA creates PRD and stories.
7. SHAKA creates architecture and ADRs.
8. ATLAS reviews security model.
9. EDISON implements one vertical slice with TDD.
10. YORK reviews code and tests.
11. BONNEY updates docs and RAG manifest.
12. LILITH deploys staging after approval.
13. STUSSY monitors health and logs.
14. STELLA summarizes final result to KUMACHII.
15. KUMACHII explains the result to the user.
```

---

## 30. Definition of Done

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
- monitoring exists
- knowledge base is updated
- human owner accepts result

---

## 31. Operating Rules Summary

```text
- KUMACHII owns personal intake and simple personal execution.
- STELLA owns workflow state and project orchestration.
- Documents are contracts.
- No phase skipping.
- No implementation before architecture approval.
- No production deploy without human approval.
- EDISON follows TDD.
- YORK reviews independently.
- ATLAS can block release.
- STUSSY can block release for missing monitoring/runbook.
- BONNEY manages documentation and RAG quality.
- Research and consultation are read-only unless explicitly assigned.
- Context must stay small and focused.
- Token optimization is mandatory for large tasks.
- Humans remain final approvers for risky decisions.
```

---

## 32. Recommended Next Iteration

1. Create dedicated profile prompt for each agent.
2. Create `AGENTS.md` from Section 21.
3. Create reusable `.skills/` files:
   - `grill-me.md`
   - `tdd.md`
   - `security-review.md`
   - `qa-review.md`
   - `deployment-check.md`
   - `rag-ingestion.md`
4. Add RAG ingestion manifest and retrieval tests.
5. Define minimum viable deployment first:
   - KUMACHII
   - STELLA
   - EDISON
   - PYTHAGORAS
   - ATLAS
   - BONNEY
6. Add YORK, LILITH, and STUSSY when project risk increases.

---

## 33. Final Notes

Keep the framework strict where risk is high.

Keep it lightweight where risk is low.

Use KUMACHII for personal flow.

Use STELLA for project control.

Use dedicated agents for isolation.

Use light agents for simple routing.

Use documents for handoff.

Use quality gates for control.

Use RAG for controlled knowledge retrieval.

Use humans for final judgment.
