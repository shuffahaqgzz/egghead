# KUMACHII-STELLA Multi-Agent Workflow & Framework

**Version:** 1.0.0  
**Status:** Deployment-Ready Reference Architecture  
**Purpose:** Best-practice multi-agent workflow for personal assistance, project delivery, software development, research, security, QA, DevOps, documentation, RAG, monitoring, and operations.  
**Style:** Compact, direct, no bluffing.  

---

## 1. Core Objective

Build a multi-agent system that is:

- structured
- role-specific
- auditable
- token-efficient
- secure by design
- deployable using isolated profiles
- controlled by quality gates
- adaptable to personal, software, infrastructure, research, security, and operations workflows

The framework combines:

1. **SOP Discipline** — alignment first, Smart Zone, vertical slicing, TDD, human review.
2. **BMAD Structure** — discovery, planning, solutioning, implementation, review, deploy, operate.
3. **OMO-Style Orchestration** — orchestrator, specialist agents, read-only consultation, background research.
4. **Hermes Runtime Isolation** — dedicated profiles, isolated credentials, isolated memory, isolated sessions.
5. **Token Optimization** — context reset, focused scope, RTK, Caveman, code-review-graph, RAG retrieval.
6. **Operational Control** — monitoring, runbooks, rollback, incident response, periodic review.

---

## 2. Design Principles

### 2.1 Alignment Before Execution

Do not execute unclear work.

Every task must define:

- intent
- scope
- out-of-scope
- constraints
- assumptions
- expected output
- success criteria
- owner agent
- reviewer agent

### 2.2 Smart Zone Discipline

Agents work better with focused context.

Rules:

- split large tasks into small tasks
- use phase-based sessions
- reset context between major phases
- use separate sessions for review
- do not load the whole codebase unless required
- use RAG retrieval instead of dumping documents
- use code-review-graph for large codebases

### 2.3 Documents as Contracts

Agent handoff must use documents.

Documents are control points, not decoration.

Required documents:

- `CONTEXT.md`
- `docs/product-brief.md`
- `docs/PRD.md`
- `docs/architecture.md`
- `docs/ADRs/*.md`
- `docs/epics/*.md`
- `docs/reviews/*.md`
- `docs/security/*.md`
- `docs/deployment.md`
- `docs/runbook.md`
- `docs/operations.md`

### 2.4 Vertical Slicing

Avoid horizontal delivery:

```text
Database first → API later → UI last
```

Use vertical delivery:

```text
One story = data + logic + interface + test + documentation impact
```

Each slice must be independently testable.

### 2.5 Test-Driven Development

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

### 2.6 Human-in-the-Loop

Human approval is mandatory for:

- scope changes
- architecture changes
- dependency additions
- security exceptions
- credential changes
- production deployment
- rollback decision
- final acceptance

### 2.7 Least Privilege

Each agent receives only the access needed for its role.

Rules:

- research agents are read-only
- security agent can block release
- QA agent cannot approve its own generated code
- DevOps agent cannot deploy production without approval
- RAG agent manages knowledge but does not silently rewrite source-of-truth docs
- monitoring agent can alert and recommend action, but cannot make risky changes without approval

---

## 3. Agent Roster

| Code Name | Role | Main Purpose | Access Level | Recommended Runtime |
|---|---|---|---|---|
| **KUMACHII** | Personal Assistant | Office work, content, education, finance, personal productivity | Medium | Light or default profile |
| **STELLA** | Orchestrator / Main Agent | Project management, workflow state, delegation, quality gates | High | Dedicated default profile |
| **SHAKA** | Architect Agent | System design, architecture, ADRs, technical decisions | High | Dedicated profile recommended |
| **EDISON** | Coding Agent | Code, app development, tests, refactoring | High | Dedicated profile |
| **PYTHAGORAS** | Research Agent | Research, comparison, evidence collection, source validation | Read-only | Dedicated profile |
| **ATLAS** | Security Advisor Agent | Threat modeling, security review, risk mitigation | High review authority | Dedicated profile |
| **YORK** | QA / Reviewer Agent | Review, QA, testing, verification, acceptance validation | Review-only + test execution | Dedicated profile recommended |
| **LILITH** | DevOps Agent | CI/CD, deployment, infrastructure, implementation procedures | High but gated | Dedicated profile |
| **BONNEY** | Documentation & RAG Knowledge Agent | Documentation, knowledge base, RAG ingestion, retrieval quality | Docs + KB write | Dedicated profile recommended |
| **STUSSY** | Monitoring & Operations Agent | Monitoring, operations, incident triage, runbooks, health checks | Ops read + limited action | Dedicated profile recommended |

---

## 4. Recommended Deployment Modes

### 4.1 Light Agent Mode

Use for low-risk, low-isolation tasks.

Best for:

- simple personal assistant tasks
- content drafting
- education support
- simple finance organization
- lightweight document formatting

Recommended light agent:

```text
KUMACHII
```

### 4.2 Dedicated Profile Mode

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

### 4.3 Hybrid Mode

Recommended default.

```text
Default profile:
  STELLA as orchestrator
  KUMACHII as personal assistant light agent

Dedicated profiles:
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

## 5. Reference Architecture

```text
┌─────────────────────────────────────────────────────────────────────┐
│                    MULTI-AGENT WORKFLOW SYSTEM                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  User / Operator                                                    │
│        │                                                            │
│        ▼                                                            │
│  STELLA — Orchestrator / Main Agent                                 │
│        │                                                            │
│        ├── KUMACHII    Personal Assistant                           │
│        ├── SHAKA       Architect                                    │
│        ├── EDISON      Coding                                       │
│        ├── PYTHAGORAS  Research                                     │
│        ├── ATLAS       Security Advisor                             │
│        ├── YORK        QA / Reviewer                                │
│        ├── LILITH      DevOps                                       │
│        ├── BONNEY      Documentation & RAG Knowledge                │
│        └── STUSSY      Monitoring & Operations                      │
│                                                                     │
├─────────────────────────────────────────────────────────────────────┤
│ Runtime Layer                                                       │
│                                                                     │
│  Hermes / Agent Runtime Profiles                                    │
│  ├── default   → STELLA + KUMACHII light routing                    │
│  ├── shaka     → Architect profile                                  │
│  ├── edison    → Coding profile                                     │
│  ├── pythagoras→ Research profile                                   │
│  ├── atlas     → Security profile                                   │
│  ├── york      → QA profile                                         │
│  ├── lilith    → DevOps profile                                     │
│  ├── bonney    → Documentation/RAG profile                          │
│  └── stussy    → Monitoring/Ops profile                             │
│                                                                     │
├─────────────────────────────────────────────────────────────────────┤
│ Knowledge & Tool Layer                                              │
│                                                                     │
│  ├── Project docs                                                   │
│  ├── RAG vector store                                               │
│  ├── Source repositories                                            │
│  ├── Issue tracker                                                  │
│  ├── CI/CD platform                                                 │
│  ├── Secrets manager                                                │
│  ├── Observability stack                                            │
│  ├── RTK                                                            │
│  ├── Caveman                                                        │
│  ├── code-review-graph                                              │
│  └── LSP / AST-grep                                                 │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 6. Agent Contracts

## 6.1 KUMACHII — Personal Assistant

**Purpose:** Assist personal and productivity tasks.

Scope:

- office work
- meeting notes
- email drafts
- content creation
- campus education
- finance organization
- personal planning

Inputs:

- user requests
- personal notes
- documents
- calendar/task data if integrated

Outputs:

- summaries
- drafts
- task lists
- study notes
- finance notes
- personal plans

Must:

- keep output practical
- separate facts from assumptions
- avoid giving financial/legal certainty without evidence
- escalate project-related work to STELLA

Must not:

- make risky financial decisions autonomously
- access private systems without explicit permission
- act as final approver for business/project delivery

---

## 6.2 STELLA — Orchestrator / Main Agent

**Purpose:** Own workflow state and task routing.

Responsibilities:

- classify requests
- determine workflow phase
- delegate tasks
- enforce quality gates
- track decisions
- prevent phase skipping
- maintain task board
- request approval when needed
- consolidate outputs from all agents

Inputs:

- user request
- current project state
- documents
- agent reports

Outputs:

- task plan
- delegation map
- status summary
- quality gate decision
- next action

Must:

- ask for clarification when execution is unsafe
- keep task scope bounded
- assign one owner and one reviewer per task
- maintain decision log
- require human approval for risky changes

Must not:

- bypass review
- approve its own risky decisions
- deploy production
- silently expand scope

---

## 6.3 SHAKA — Architect Agent

**Purpose:** Own system and solution architecture.

Responsibilities:

- system design
- component boundaries
- data flow
- API contracts
- integration design
- infrastructure architecture
- ADR creation
- implementation readiness check

Inputs:

- PRD
- constraints
- existing architecture
- research report
- security requirements

Outputs:

- `docs/architecture.md`
- `docs/ADRs/*.md`
- architecture diagrams
- readiness report
- technical risks

Must:

- define clear boundaries
- explain trade-offs
- include security and operability
- design for testability
- hand off implementable stories to EDISON

Must not:

- write production code as primary owner
- ignore security constraints
- approve implementation without readiness check

---

## 6.4 EDISON — Coding Agent

**Purpose:** Implement stories with tests.

Responsibilities:

- read story and architecture
- write tests first
- implement minimal solution
- refactor carefully
- run tests
- create atomic commits
- document code impact

Inputs:

- approved story
- architecture
- ADRs
- test requirements

Outputs:

- source code
- tests
- implementation notes
- commit references

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

---

## 6.5 PYTHAGORAS — Research Agent

**Purpose:** Provide evidence-based research.

Responsibilities:

- collect sources
- compare options
- summarize findings
- identify uncertainty
- support decisions with citations
- produce research notes

Inputs:

- research question
- constraints
- target decision

Outputs:

- research report
- comparison table
- recommendation
- source list
- uncertainty notes

Must:

- cite sources
- distinguish fact from inference
- use current sources when freshness matters
- provide practical recommendation

Must not:

- fabricate sources
- modify code
- make final architecture decision
- provide unsupported claims

---

## 6.6 ATLAS — Security Advisor Agent

**Purpose:** Identify and reduce security risk.

Responsibilities:

- threat modeling
- credential review
- access control review
- dependency risk review
- security architecture review
- deployment risk review
- compliance checklist support

Inputs:

- architecture
- code diff
- deployment plan
- dependency list
- secrets model

Outputs:

- threat model
- risk matrix
- mitigation plan
- security review report
- release block recommendation

Must:

- use severity levels
- block critical risks
- recommend concrete mitigations
- review secrets and access scope

Must not:

- approve risky exceptions alone
- expose secrets in reports
- perform destructive testing without approval

---

## 6.7 YORK — QA / Reviewer Agent

**Purpose:** Validate correctness and quality.

Responsibilities:

- review code
- verify tests
- check acceptance criteria
- run QA checklist
- identify regression risk
- validate documentation impact

Inputs:

- PRD
- story
- code diff
- test result
- architecture

Outputs:

- QA report
- defect list
- acceptance decision
- retest result

Must:

- review independently
- verify acceptance criteria
- check edge cases
- report defects clearly

Must not:

- review its own implementation as final approval
- ignore failed tests
- approve incomplete stories

---

## 6.8 LILITH — DevOps Agent

**Purpose:** Build, deploy, verify, rollback.

Responsibilities:

- environment setup
- CI/CD configuration
- deployment plan
- smoke tests
- rollback procedure
- release checklist
- commissioning test

Inputs:

- reviewed code
- deployment target
- infrastructure constraints
- secrets requirements

Outputs:

- CI/CD config
- deployment report
- smoke test result
- rollback plan
- commissioning report

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

---

## 6.9 BONNEY — Documentation & RAG Knowledge Agent

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

Inputs:

- source docs
- architecture
- code changes
- research reports
- operational notes

Outputs:

- documentation updates
- RAG ingestion manifest
- retrieval test report
- knowledge freshness report

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

---

## 6.10 STUSSY — Monitoring & Operations Agent

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

Inputs:

- logs
- metrics
- traces
- alerts
- runbooks
- deployment reports

Outputs:

- health report
- incident summary
- root cause hypothesis
- remediation checklist
- monitoring improvement notes

Must:

- report severity clearly
- separate evidence from hypothesis
- escalate production risk
- preserve incident timeline

Must not:

- perform destructive remediation without approval
- hide recurring errors
- mark incident resolved without verification

---

## 7. Workflow Lifecycle

## Phase 0 — Intake & Discovery

**Owner:** STELLA  
**Support:** KUMACHII, PYTHAGORAS  
**Reviewer:** User

Goal:

- understand request
- define scope
- identify risks
- choose workflow path

Activities:

1. classify task type
2. clarify intent
3. define scope and out-of-scope
4. list assumptions
5. identify affected systems
6. select owner agents
7. create product brief if project-level

Outputs:

- `docs/product-brief.md`
- `CONTEXT.md`
- task classification
- agent assignment

Gate 0:

- [ ] problem is clear
- [ ] goal is clear
- [ ] scope is bounded
- [ ] assumptions are listed
- [ ] owner agent is assigned
- [ ] reviewer agent is assigned

---

## Phase 1 — Planning

**Owner:** STELLA  
**Support:** KUMACHII, PYTHAGORAS, BONNEY  
**Reviewer:** User

Goal:

- define what must be delivered

Activities:

1. create PRD
2. define user stories
3. define acceptance criteria
4. define NFRs
5. create initial risks
6. prepare documentation structure

Outputs:

- `docs/PRD.md`
- `docs/epics/*.md`
- `docs/risks.md`

Gate 1:

- [ ] PRD exists
- [ ] stories exist
- [ ] acceptance criteria exist
- [ ] NFRs exist
- [ ] out-of-scope is listed
- [ ] success criteria are measurable

---

## Phase 2 — Solutioning & Architecture

**Owner:** SHAKA  
**Support:** PYTHAGORAS, ATLAS, BONNEY  
**Reviewer:** STELLA, User

Goal:

- define how the solution will be built

Activities:

1. create architecture
2. define components
3. define data flow
4. define API contracts
5. define storage model
6. define security model
7. define deployment model
8. create ADRs
9. run implementation readiness check

Outputs:

- `docs/architecture.md`
- `docs/ADRs/*.md`
- readiness report

Gate 2:

- [ ] architecture exists
- [ ] ADRs exist for major decisions
- [ ] component boundaries are clear
- [ ] data flow is clear
- [ ] security model is defined
- [ ] deployment model is defined
- [ ] implementation readiness is PASS

---

## Phase 3 — Implementation

**Owner:** EDISON  
**Support:** SHAKA, PYTHAGORAS, BONNEY  
**Reviewer:** YORK

Goal:

- build story by story with tests

Per-story workflow:

```text
1. Read story
2. Read related architecture section
3. Write failing test
4. Implement minimum code
5. Run test
6. Fix failure
7. Refactor only if needed
8. Run relevant test suite
9. Update docs if needed
10. Create atomic commit
11. Hand off to YORK
```

Outputs:

- source code
- tests
- implementation notes
- commit reference

Gate 3:

- [ ] story complete
- [ ] tests pass
- [ ] acceptance criteria satisfied
- [ ] no hardcoded secrets
- [ ] no unapproved dependency
- [ ] architecture boundaries respected
- [ ] documentation impact handled

---

## Phase 4 — Review, QA & Security

**Owner:** YORK  
**Support:** ATLAS, SHAKA, BONNEY  
**Reviewer:** STELLA, User

Goal:

- validate correctness, quality, security, and documentation

Activities:

1. code review
2. test review
3. acceptance criteria review
4. security review
5. architecture compliance review
6. documentation review
7. defect logging
8. retest after fixes

Outputs:

- `docs/reviews/code-review-*.md`
- `docs/security/security-review-*.md`
- defect list
- acceptance decision

Gate 4:

- [ ] code review passed
- [ ] security review passed
- [ ] critical issues fixed
- [ ] regression tests pass
- [ ] docs updated
- [ ] human QA completed where required

---

## Phase 5 — Deployment

**Owner:** LILITH  
**Support:** ATLAS, STUSSY, BONNEY  
**Reviewer:** STELLA, User

Goal:

- release safely and verify deployment

Activities:

1. build artifact
2. run pre-deploy checks
3. validate secrets and config
4. deploy to staging
5. run smoke tests
6. request production approval
7. deploy production
8. run post-deploy checks
9. prepare rollback report

Outputs:

- `docs/deployment.md`
- deployment report
- smoke test result
- rollback note

Gate 5:

- [ ] build passed
- [ ] staging deployment passed
- [ ] smoke test passed
- [ ] rollback path exists
- [ ] monitoring active
- [ ] production approval exists

---

## Phase 6 — Operate, Monitor & Improve

**Owner:** STUSSY  
**Support:** LILITH, ATLAS, BONNEY  
**Reviewer:** STELLA

Goal:

- keep system healthy and improve continuously

Activities:

1. monitor services
2. review logs
3. detect incidents
4. classify severity
5. recommend remediation
6. update runbooks
7. update RAG knowledge
8. create post-incident report

Outputs:

- `docs/operations.md`
- `docs/runbook.md`
- incident report
- monitoring report
- improvement backlog

Gate 6:

- [ ] system health known
- [ ] alerts configured
- [ ] runbook updated
- [ ] incidents reviewed
- [ ] recurring issues tracked
- [ ] knowledge base updated

---

## 8. Delegation Matrix

| Task Type | Owner | Support | Reviewer | Approval Needed |
|---|---|---|---|---|
| Personal task | KUMACHII | BONNEY | User | User if sensitive |
| Project planning | STELLA | PYTHAGORAS, BONNEY | User | Yes |
| Architecture | SHAKA | PYTHAGORAS, ATLAS | STELLA | Yes |
| Coding | EDISON | SHAKA, BONNEY | YORK | For merge |
| Research | PYTHAGORAS | BONNEY | STELLA | If decision-impacting |
| Security review | ATLAS | SHAKA, LILITH | STELLA | Yes for exceptions |
| QA/testing | YORK | EDISON, ATLAS | STELLA | Yes for release |
| Deployment | LILITH | ATLAS, STUSSY | STELLA | Yes for production |
| Documentation | BONNEY | All agents | YORK | If source-of-truth changes |
| Monitoring | STUSSY | LILITH, ATLAS | STELLA | For remediation |

---

## 9. Handoff Protocol

Every handoff must include:

```markdown
# Agent Handoff

From:
To:
Task ID:
Phase:
Objective:
Inputs:
Files touched:
Decisions made:
Assumptions:
Open risks:
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

---

## 10. Runtime Profile Mapping

Recommended profile structure:

```text
~/.hermes/
├── auth.json
├── config.yaml
├── .env
├── hermes-agent/
├── profiles/
│   ├── shaka/
│   │   ├── auth.json
│   │   ├── config.yaml
│   │   ├── .env
│   │   ├── memory/
│   │   ├── sessions/
│   │   └── skills/
│   ├── edison/
│   ├── pythagoras/
│   ├── atlas/
│   ├── york/
│   ├── lilith/
│   ├── bonney/
│   └── stussy/
└── cron/
```

Rules:

- each profile has isolated `auth.json`
- each profile has isolated `config.yaml`
- each profile has isolated `.env`
- each profile has isolated `memory/`
- each profile has isolated `sessions/`
- each profile has isolated `skills/`
- credentials are copied intentionally, never assumed shared

---

## 11. Gateway Rules

### 11.1 Token Rule

One bot token can be used by one gateway only.

Do not reuse Telegram or Discord tokens across profiles.

### 11.2 Platform Rule

Recommended:

```text
STELLA / default: Telegram + Discord
Dedicated agents: Discord only
```

### 11.3 Environment Variable Rule

Do not set unused token variables to empty strings.

Use comments:

```bash
# TELEGRAM_BOT_TOKEN=
# TELEGRAM_ALLOWED_USERS=
# TELEGRAM_HOME_CHANNEL=
```

### 11.4 Provider Switching Rule

When switching model provider:

```bash
<profile> config set model.base_url ""
<profile> config set model.api_key ""
systemctl --user restart hermes-gateway-<profile>.service
```

---

## 12. Recommended Model Strategy

Use model specialization.

| Agent | Model Characteristics Needed |
|---|---|
| KUMACHII | balanced, multilingual, personal productivity, summarization |
| STELLA | strongest reasoning, planning, long-context coordination |
| SHAKA | strong architecture reasoning, diagram understanding, trade-off analysis |
| EDISON | strong coding, tool use, debugging, test generation |
| PYTHAGORAS | strong research, citations, long reading, source comparison |
| ATLAS | strong security reasoning, risk analysis, threat modeling |
| YORK | strict reviewer, test reasoning, edge-case detection |
| LILITH | DevOps, shell, YAML, CI/CD, Kubernetes, infrastructure reasoning |
| BONNEY | documentation, RAG, summarization, information architecture |
| STUSSY | log analysis, monitoring, incident summarization, ops reasoning |

Rules:

- do not use one model for all roles if cost and quality matter
- use cheaper fast models for simple retrieval and grep
- use strongest reasoning model for gates and architecture decisions
- use read-only strong model for independent review

---

## 13. Tooling Strategy

### 13.1 Required Tools

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

### 13.2 Recommended AI Tools

| Tool | Purpose |
|---|---|
| RTK | compress command output |
| Caveman | compact agent responses |
| code-review-graph | code context and blast radius |
| LSP | deterministic code navigation |
| AST-grep | structural code search and refactor |
| MCP tools | controlled external integrations |
| Vector DB | RAG retrieval |

---

## 14. RAG Architecture for BONNEY

### 14.1 Knowledge Source Hierarchy

Priority order:

1. source-of-truth documents
2. architecture and ADRs
3. PRD and acceptance criteria
4. code and tests
5. deployment/runbook docs
6. research reports
7. generated summaries
8. chat history

Generated summaries must not override primary sources.

### 14.2 RAG Pipeline

```text
Ingest → Clean → Chunk → Tag → Embed → Index → Retrieve → Rerank → Answer → Cite → Validate
```

### 14.3 Metadata Required

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

### 14.4 RAG Rules

- never ingest secrets
- never ingest raw credentials
- mark stale content
- cite source documents
- keep chunk size consistent
- use retrieval tests for important docs
- re-index after major documentation changes

### 14.5 Retrieval Test Template

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

## 15. Security Model

### 15.1 Isolation

Each dedicated agent must have:

- isolated profile
- isolated credentials
- isolated memory
- isolated sessions
- isolated skills
- dedicated gateway service
- least-privilege tool access

### 15.2 Credential Rules

- never commit credentials
- never paste secrets into prompt files
- use `.env` or secret manager
- rotate leaked credentials
- copy credentials intentionally
- keep credentials role-specific

### 15.3 Tool Access Rules

| Agent | Tool Access |
|---|---|
| KUMACHII | personal productivity tools only |
| STELLA | orchestration, issue tracker, read status |
| SHAKA | docs, diagrams, repo read, architecture tools |
| EDISON | repo write, test runner, code tools |
| PYTHAGORAS | web/research/read-only docs |
| ATLAS | security scanners, dependency audit, read-only infra unless approved |
| YORK | test runner, review tools, read/write review reports |
| LILITH | CI/CD, deployment tools, infra tools, gated production access |
| BONNEY | docs and knowledge base tools |
| STUSSY | logs, metrics, traces, alerting tools, gated remediation |

### 15.4 Release Blocking

ATLAS can block release for:

- exposed secret
- critical vulnerability
- broken access control
- unsafe deployment config
- missing rollback for high-risk production change

YORK can block release for:

- failed acceptance criteria
- failed tests
- unreviewed critical code
- incomplete documentation for user-impacting change

STUSSY can block release for:

- missing monitoring
- missing runbook
- recurring unresolved production incident
- failed smoke test

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

## 17. Project File Structure

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
│   ├── ADRs/
│   │   └── 000-template.md
│   ├── epics/
│   │   └── epic-000-template.md
│   ├── reviews/
│   │   └── review-000-template.md
│   ├── security/
│   │   └── security-review-000-template.md
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

## 18. AGENTS.md Operating Guide

Copy this into project `AGENTS.md`.

```markdown
# AI Agent Operating Guide

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

- KUMACHII: personal assistant
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

## 19. Commissioning Checklist

### 19.1 Infrastructure

- [ ] server accessible
- [ ] SSH working
- [ ] Python 3.11+ available
- [ ] systemd user service available
- [ ] network to providers available
- [ ] enough RAM available
- [ ] time sync active

### 19.2 Runtime

- [ ] Hermes installed
- [ ] virtual environment available
- [ ] CLI works
- [ ] default profile exists
- [ ] dedicated profiles created
- [ ] profile aliases configured

### 19.3 Configuration

- [ ] global config exists
- [ ] global auth exists
- [ ] global `.env` exists
- [ ] profile config exists
- [ ] profile auth exists
- [ ] profile `.env` exists
- [ ] credentials copied intentionally
- [ ] model provider validated

### 19.4 Platform

- [ ] Telegram token only used by default profile
- [ ] Discord token unique per profile
- [ ] channel IDs configured
- [ ] bot permissions validated
- [ ] unused token vars commented out

### 19.5 Services

- [ ] systemd services installed
- [ ] services enabled
- [ ] services started
- [ ] restart policy active
- [ ] logs clean

---

## 20. Testing Checklist

### 20.1 Gateway Status

```bash
systemctl --user status hermes-gateway-default.service
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

### 20.2 Chat Test

```bash
hermes chat -q "ping - respond with STELLA online"
shaka chat -q "ping - respond with SHAKA online"
edison chat -q "ping - respond with EDISON online"
pythagoras chat -q "ping - respond with PYTHAGORAS online"
atlas chat -q "ping - respond with ATLAS online"
york chat -q "ping - respond with YORK online"
lilith chat -q "ping - respond with LILITH online"
bonney chat -q "ping - respond with BONNEY online"
stussy chat -q "ping - respond with STUSSY online"
```

### 20.3 Model Test

```bash
<profile> chat -q "what model are you using? respond with model name only"
```

### 20.4 Credential Isolation Test

```bash
cat ~/.hermes/profiles/<profile>/auth.json | python3 -c "import sys,json; d=json.load(sys.stdin); print(list(d.get('credential_pool',{}).keys()))"
```

Expected:

- each profile only has required provider credentials
- no unrelated credentials present

### 20.5 Token Conflict Test

```bash
journalctl --user -u hermes-gateway-<profile>.service --since "10 minutes ago" --no-pager | grep -i "token"
```

Expected:

```text
No token already in use error
```

---

## 21. Operations Runbook

### 21.1 Check All Profiles

```bash
hermes profile list
```

### 21.2 Check Gateway Logs

```bash
journalctl --user -u hermes-gateway-<profile>.service -f
```

### 21.3 Restart One Gateway

```bash
systemctl --user restart hermes-gateway-<profile>.service
```

### 21.4 Stop One Agent

```bash
systemctl --user stop hermes-gateway-<profile>.service
systemctl --user disable hermes-gateway-<profile>.service
```

### 21.5 Full Rollback to Default Only

```bash
systemctl --user stop hermes-gateway-shaka.service
systemctl --user stop hermes-gateway-edison.service
systemctl --user stop hermes-gateway-pythagoras.service
systemctl --user stop hermes-gateway-atlas.service
systemctl --user stop hermes-gateway-york.service
systemctl --user stop hermes-gateway-lilith.service
systemctl --user stop hermes-gateway-bonney.service
systemctl --user stop hermes-gateway-stussy.service

systemctl --user restart hermes-gateway-default.service
```

---

## 22. Troubleshooting

### 22.1 Gateway Will Not Start

Check:

```bash
journalctl --user -u hermes-gateway-<profile>.service -l --no-pager
```

Common causes:

- token conflict
- missing credential
- wrong provider config
- bad `.env`
- systemd user session issue
- model provider unreachable

### 22.2 Token Already in Use

Cause:

```text
same bot token used by multiple gateways
```

Fix:

- use unique token per profile
- disable platform on secondary profile
- restart affected service

### 22.3 Chat Timeout

Check:

```bash
<profile> gateway health
<profile> config get model.provider
<profile> config get model.default
cat ~/.hermes/profiles/<profile>/auth.json
```

### 22.4 Wrong Model Used

Fix:

```bash
<profile> config set model.base_url ""
<profile> config set model.api_key ""
systemctl --user restart hermes-gateway-<profile>.service
```

### 22.5 Credential Not Found

Check:

```bash
cat ~/.hermes/profiles/<profile>/auth.json | python3 -m json.tool
```

Fix:

- copy required credential into profile `auth.json`
- restart gateway

### 22.6 RAG Gives Wrong Answer

Check:

- source priority
- stale chunks
- duplicate summaries
- missing metadata
- wrong document version
- retrieval test failure

Fix:

- re-ingest source documents
- mark stale content
- rerun retrieval tests
- update chunk metadata

---

## 23. Token Optimization Strategy

### 23.1 Context Rules

- keep task scope small
- avoid dumping entire repo
- reset context at phase boundaries
- use separate review sessions
- summarize before handoff
- use RAG for retrieval

### 23.2 RTK

Use for compressed command output.

Best for:

- `git status`
- `git diff`
- `git log`
- `docker ps`
- `kubectl get`
- `npm test`
- `cargo test`

### 23.3 Caveman

Use for compact repeated agent responses.

Best for:

- frequent status reports
- long coding sessions
- agent-to-agent summaries

### 23.4 code-review-graph

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

---

## 24. Minimal Deployment Plan

```text
1. Install Hermes runtime.
2. Create default profile for STELLA.
3. Add KUMACHII as light personal assistant or default sub-agent.
4. Create dedicated profiles: shaka, edison, pythagoras, atlas, york, lilith, bonney, stussy.
5. Disable Telegram on dedicated profiles.
6. Configure unique Discord token per dedicated profile.
7. Configure model per profile.
8. Copy required credentials intentionally.
9. Install systemd services.
10. Start default gateway.
11. Start dedicated gateways.
12. Run chat tests.
13. Run model tests.
14. Run credential isolation tests.
15. Run token conflict checks.
16. Run quality gate dry-run.
17. Document final status.
```

---

## 25. Definition of Done

A task is done only when:

- requirement is satisfied
- acceptance criteria pass
- tests pass
- review passed
- security checked if relevant
- docs updated if relevant
- deployment verified if relevant
- monitoring updated if relevant
- user-facing summary is provided

A project is done only when:

- all planned stories are complete
- all quality gates pass
- deployment works
- rollback exists
- monitoring works
- runbook exists
- RAG knowledge is updated
- human owner accepts result

---

## 26. Operating Rules Summary

```text
- STELLA owns workflow state.
- KUMACHII handles personal productivity.
- SHAKA owns architecture.
- EDISON owns implementation.
- PYTHAGORAS owns research evidence.
- ATLAS owns security risk review.
- YORK owns QA and acceptance validation.
- LILITH owns deployment and rollback.
- BONNEY owns documentation and RAG quality.
- STUSSY owns monitoring and operations.
- Documents are contracts.
- No phase skipping.
- No production deployment without approval.
- No unapproved dependency additions.
- No hardcoded secrets.
- No unsupported claims.
- Context must stay small and focused.
- Security and QA can block release.
- Human owns final acceptance.
```

---

## 27. Recommended Next Iteration

After initial deployment, improve in this order:

1. Add STELLA task board and decision log.
2. Add BONNEY RAG ingestion and retrieval tests.
3. Add YORK review templates.
4. Add ATLAS security checklist.
5. Add LILITH deployment checklist.
6. Add STUSSY monitoring dashboard.
7. Add code-review-graph for large repositories.
8. Add automated quality gate reports.
9. Add periodic knowledge freshness review.
10. Add incident postmortem workflow.

