# AI-Driven Development Workflow: Integrated Framework

> **Purpose:** A practical, agent-readable operating guide that merges the strongest elements of four AI development frameworks into a unified, actionable workflow.

**Sources Analyzed:**
1. [mattpocock/skills](https://github.com/mattpocock/skills) — Composable skill-based agent instructions
2. [forrestchang/andrej-karpathy-skills](https://github.com/forrestchang/andrej-karpathy-skills) — Karpathy's coding principles as reusable skills
3. [BMAD-METHOD](https://github.com/bmad-code-org/BMAD-METHOD) — Structured phase-gated AI-native development methodology
4. [oh-my-openagent](https://github.com/code-yeongyu/oh-my-openagent) — Multi-model agent orchestration for software teams

---

## 1. Framework Comparison

### 1.1 Overview Table

| Dimension | Matt Pocock Skills | Karpathy Skills | BMAD-METHOD | Oh-My-OpenAgent |
|-----------|-------------------|-----------------|-------------|-----------------|
| **Type** | Skill instruction files | Coding principle skills | Full development methodology | Agent orchestration plugin |
| **Format** | Markdown `.md` files | Markdown skill files | Markdown docs + agent prompts + templates | TypeScript plugin (OpenCode) |
| **Scope** | Individual task guidance | Individual coding behavior | Full SDLC (idea → deploy) | Runtime agent coordination |
| **Agent Model** | Single generalist agent | Single generalist agent | 6 specialized role-agents | 10 multi-model agents |
| **Process Rigor** | Low (flexible) | Low (principles, not process) | High (phases, gates, templates) | Medium (hook-driven, workflow-aware) |
| **Composability** | High — skills reference skills | Medium — independent principles | Low — sequential phases | High — agents call agents, skills load skills |
| **Human-in-Loop** | Strong (grilling, approval) | Implicit (developer runs agent) | Strong (gated reviews) | Medium (todo enforcer, but autonomous) |
| **Model Dependency** | Agent-agnostic | Agent-agnostic | Agent-agnostic | Multi-model (Claude, GPT, Gemini, Grok) |
| **Installation** | `npx skills@latest add` | Manual copy / skill loader | Skill-based install | `npx oh-my-opencode@latest` (OpenCode plugin) |

### 1.2 Detailed Analysis

#### Matt Pocock Skills

**Purpose:** Small, composable markdown instruction files that fix specific failure modes in AI coding agents — misalignment, verbosity, premature coding, and lack of rigor.

**Strengths:**
- Dead-simple format: pure markdown, no runtime, no schema
- Agent-agnostic: works with Claude Code, Cursor, Codex, any tool that reads markdown
- Composable: skills invoke other skills (e.g., `grill-with-docs` → `to-prd`)
- Solves real failure modes: `/grill-me` (misalignment), `CONTEXT.md` (verbosity), `/tdd` (premature coding), `/triage` (issue management)
- Shared language via `CONTEXT.md` — reduces agent verbosity and ensures consistent naming
- Based on decades of engineering experience, not theory

**Limitations:**
- No enforcement mechanism — agent can ignore rules
- No runtime/state — skills are static text
- Quality depends on model instruction-following ability
- No formal validation or schema checker for skill files
- No cross-session state tracking

**Best Use Cases:**
- Fixing specific agent failure modes (misalignment, verbosity, slop)
- Daily engineering tasks with a coding agent
- Building a shared project vocabulary (CONTEXT.md)
- TDD-driven feature development
- Issue triage and PRD creation

**Key Skills:**
| Skill | Purpose |
|-------|---------|
| `/grill-me` | Grilling session — align intent before coding |
| `/grill-with-docs` | Grilling + build shared language + ADRs |
| `/triage` | Turn issues into agent-ready briefs |
| `/to-prd` | Convert rough ideas into PRD |
| `/tdd` | Test-driven development workflow |
| `/diagnose` | Systematic debugging |
| `/prototype` | Quick prototype (UI or logic) |
| `/improve-codebase-architecture` | Deepen modules, redesign interfaces |
| `/zoom-out` | Step back and reassess direction |
| `/write-a-skill` | Create new skills |

#### Karpathy Skills

**Purpose:** Encode Andrej Karpathy's pragmatic, simplicity-first coding philosophy into loadable skill definitions for AI agents.

**Strengths:**
- Battle-tested principles from real ML/systems engineering
- Anti-pattern documentation — each skill lists what NOT to do
- Example-driven — concrete code examples make principles actionable
- Human-compatible — principles work for both humans AND AI agents
- Composable — skills load independently

**Limitations:**
- Python/ML bias — less applicable to enterprise Java/C# systems
- Small-project bias — "baby steps" and "no premature abstraction" suit scripts, not large systems
- Solo-developer bias — optimized for individual workflows
- Some principles conflict with structured methodology (e.g., "start from scratch" vs. BMAD's traceability)
- "Print debugging" doesn't scale to distributed systems

**Best Use Cases:**
- Individual or small-team Python/ML projects
- Prototyping and experimentation phases
- Teaching AI agents to write cleaner, simpler code
- Preventing over-engineering and premature abstraction

**Key Principles:**
| Principle | Core Rule |
|-----------|-----------|
| Baby Steps | Make one small change, verify, repeat |
| Simplicity First | Prefer flat code over class hierarchies |
| Code by Example | Start with MWE, then generalize |
| Verify Everything | Assert shapes, print debug, check after every op |
| No Premature Abstraction | Don't abstract until pattern appears 3+ times |
| Readability Over Cleverness | Obvious code > elegant one-liners |
| Always Be Running | Keep code runnable, fix breaks immediately |
| Software 2.0 Mindset | Dataset IS the code, parameters > logic |

#### BMAD-METHOD

**Purpose:** A comprehensive, phase-gated, role-specialized AI development methodology that uses structured documents as inter-agent contracts. Stands for "Breakthrough Method of Agile AI-Driven Development."

**Strengths:**
- Reduced hallucination/drift — phased, document-driven approach constrains agents
- Scalable to complex projects — architecture is thought through before code
- Full traceability — every task traces back to a user story → PRD requirement
- Reproducible — same PRD + methodology = consistent results
- Human-friendly — quality gates are natural review checkpoints
- Prescribed templates and directory structure
- Named agents with personalities (Mary the Analyst, John the PM, Winston the Architect, Amelia the Dev, Sally the UX Designer, Paige the Tech Writer)

**Limitations:**
- Overhead for small projects — full BMAD process is overkill for trivial tasks
- Sequential bottleneck — strict phase ordering prevents parallelism
- Document maintenance burden — keeping all phase docs in sync requires discipline
- No runtime orchestration — it's a methodology, not a tool
- Quality depends on agent prompt definitions

**Best Use Cases:**
- Building new applications from scratch (greenfield)
- Complex multi-module systems requiring upfront architecture
- Teams that need traceability and documentation
- Projects requiring formal review/approval workflows

**Key Phases:**
| Phase | Owner | Output | Gate |
|-------|-------|--------|------|
| 1. Analysis | Analyst (Mary) | Product brief, research reports, brainstorming report | Brief completeness |
| 2. Planning | PM (John) | PRD, UX spec | PRD completeness + acceptance criteria |
| 3. Solutioning | Architect (Winston) | Architecture doc + ADRs, Epics/Stories | Implementation readiness check |
| 4. Implementation | Developer (Amelia) | Source code, tests, review reports | Code quality gates, test pass |

#### Oh-My-OpenAgent (Oh-My-OpenCode)

**Purpose:** A multi-model agent orchestration plugin for OpenCode that provides specialized AI agents, background task execution, LSP/AST tools, and a full Claude Code compatibility layer.

**Strengths:**
- True multi-model orchestration — different models for different tasks (Claude Opus for planning, GPT-5.2 for debugging, Gemini for visual, Grok for fast grep)
- Background agent execution — parallel task processing like a real dev team
- 31 lifecycle hooks — fine-grained control over agent behavior
- LSP + AST-Grep tools — deterministic, safe refactoring
- Todo Continuation Enforcer — agent can't quit halfway
- Comment Checker — prevents AI slop in comments
- Built-in skills (playwright, frontend-ui-ux, git-master)
- Curated MCPs (Exa search, Context7 docs, Grep.app)
- Sisyphus discipline agent — rolls the boulder until the job is done

**Limitations:**
- Tied to OpenCode runtime — not agent-agnostic
- TypeScript/Bun ecosystem dependency
- Complex configuration (JSONC config with 25+ hook toggles)
- High token consumption — orchestrator + subagents consume context fast
- Claude OAuth restrictions (Anthropic blocked third-party OAuth)
- Requires multiple API keys for full multi-model experience
- Opinionated defaults — may not suit all workflows

**Best Use Cases:**
- Large-scale feature development requiring parallel work
- Multi-model teams (use best model per task type)
- Projects needing deterministic refactoring via LSP
- Long-running autonomous coding sessions
- Teams already using OpenCode

**Key Agents:**
| Agent | Model | Purpose |
|-------|-------|---------|
| Sisyphus | Claude Opus 4.5 | Primary orchestrator, todo-driven |
| Atlas | Claude Opus 4.5 | Master orchestration hook |
| Oracle | GPT-5.2 | Read-only architecture/debug consultation |
| Librarian | Big Pickle | Multi-repo research, docs search |
| Explore | GPT-5 Nano | Fast contextual grep |
| Multimodal Looker | Gemini 3 Flash | PDF/image analysis |
| Prometheus | Claude Opus 4.5 | Strategic planning (interview mode) |
| Metis | Claude Sonnet 4.5 | Pre-planning analysis |
| Momus | Claude Sonnet 4.5 | Plan validation/review |
| Sisyphus-Junior | Claude Sonnet 4.5 | Category-spawned task executor |

### 1.3 Overlapping Concepts

| Concept | Matt Pocock | Karpathy | BMAD | OMO |
|---------|-------------|----------|------|-----|
| Think before code | `/grill-me` alignment | Baby steps, plan first | Phase-gated planning | Prometheus planning, Metis analysis |
| Test-driven quality | `/tdd` skill | Verify everything | QA gate in Phase 4 | Todo enforcer, comment checker |
| Specialized roles | Implicit (user as PM) | Solo developer | 6 named agents | 10 specialized agents |
| Document-driven | PRD, CONTEXT.md, ADRs | Self-documenting code | Phase artifacts (brief, PRD, arch) | AGENTS.md injection |
| Simplicity | Zoom-out, prototype | Core principle | Controlled via architecture phase | Sisyphus discipline |
| Issue tracking | `/triage`, `/to-issues` | Not addressed | Epic → Story → Task | Background task management |

### 1.4 Unique Contributions

| Framework | Unique Contribution |
|-----------|-------------------|
| **Matt Pocock Skills** | Grilling sessions for intent alignment; shared language (CONTEXT.md) for concision; composable skill architecture |
| **Karpathy Skills** | Anti-pattern documentation; incremental baby-step verification; Software 2.0 mindset; print-debug legitimacy |
| **BMAD-METHOD** | Phase-gated quality gates; named agent personas; full traceability (requirement → story → code); implementation readiness check |
| **Oh-My-OpenAgent** | Multi-model runtime orchestration; background parallel agents; LSP/AST deterministic refactoring; 31 lifecycle hooks; todo continuation enforcer |

---

## 2. Integrated Workflow Design

### 2.1 Design Principles

The integrated workflow merges:
- **BMAD's structure** (phases, gates, traceability) as the backbone
- **Matt Pocock's alignment techniques** (grilling, shared language) at the start of each phase
- **Karpathy's coding principles** (baby steps, simplicity, verify) during implementation
- **Oh-My-OpenAgent's orchestration** (multi-model agents, background tasks, LSP tools) as the runtime engine

### 2.2 Full Development Lifecycle

```
┌─────────────────────────────────────────────────────────────────────┐
│                    AI-DRIVEN DEVELOPMENT LIFECYCLE                  │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│  Phase 0: DISCOVERY          Phase 1: PLANNING                      │
│  ├─ Grill Session            ├─ Create PRD                          │
│  ├─ Domain Research          ├─ UX Design                           │
│  ├─ Product Brief            ├─ Create CONTEXT.md                   │
│  └─ Gate: Brief Approved     └─ Gate: PRD + UX Approved            │
│                                                                     │
│  Phase 2: SOLUTIONING        Phase 3: IMPLEMENTATION                │
│  ├─ Architecture + ADRs     ├─ Story-by-story dev (baby steps)      │
│  ├─ Epic & Story Creation   ├─ TDD per story                       │
│  ├─ Implementation Readiness ├─ Parallel background agents           │
│  └─ Gate: Architecture Pass └─ Gate: All Stories Done + Tests Pass  │
│                                                                     │
│  Phase 4: REVIEW & DEPLOY                                            │
│  ├─ Code Review (Oracle/QA)                                          │
│  ├─ Documentation                                                     │
│  ├─ Deployment                                                        │
│  └─ Gate: Smoke Tests Pass                                           │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### 2.3 Phase Details with Agent Collaboration

#### Phase 0: Discovery & Alignment

**Goal:** Understand the problem, align intent, build shared vocabulary.

| Step | Activity | Agent/Role | Technique | Output |
|------|----------|------------|-----------|--------|
| 0.1 | Grilling session — interrogate user about what they want | **Planner** (interview mode) | Matt Pocock `/grill-me` | Aligned intent document |
| 0.2 | Build shared language | **Planner** + User | `/grill-with-docs` → CONTEXT.md | `CONTEXT.md` |
| 0.3 | Domain/market research | **Researcher** (background) | BMAD research skills | Research findings |
| 0.4 | Product brief | **Analyst** | BMAD `bmad-product-brief` | `product-brief.md` |
| 0.5 | **Quality Gate 0** | User | Brief completeness check | ✅ Proceed / ❌ Revise |

**Agent Collaboration:**
- Planner runs interview mode (like OMO's Prometheus)
- Researcher fires background agents for parallel domain/market/technical research (like OMO's Librarian)
- User and Planner co-create CONTEXT.md for shared vocabulary

#### Phase 1: Planning

**Goal:** Define what to build, for whom, and how it should look/feel.

| Step | Activity | Agent/Role | Technique | Output |
|------|----------|------------|-----------|--------|
| 1.1 | Create PRD from brief | **Product Manager** | BMAD `bmad-create-prd` | `PRD.md` |
| 1.2 | Validate PRD | **Plan Reviewer** | OMO's Momus review | PRD review report |
| 1.3 | UX Design (if applicable) | **UX Designer** | BMAD `bmad-create-ux-design` | `ux-spec.md` |
| 1.4 | **Quality Gate 1** | User + PM | PRD completeness checklist | ✅ Proceed / ❌ Revise |

**Agent Collaboration:**
- PM creates PRD using BMAD templates
- Plan Reviewer (Momus equivalent) validates for clarity, verifiability, completeness
- UX Designer creates UX spec if UI is involved

#### Phase 2: Solutioning

**Goal:** Decide how to build it, break work into implementable units.

| Step | Activity | Agent/Role | Technique | Output |
|------|----------|------------|-----------|--------|
| 2.1 | Architecture design | **Architect** | BMAD `bmad-create-architecture` | `architecture.md` + ADRs |
| 2.2 | Architecture consultation | **Oracle** (background) | OMO's Oracle | Architecture review |
| 2.3 | Create epics & stories | **PM** + Architect | BMAD `bmad-create-epics-and-stories` | Epic files with stories |
| 2.4 | Implementation readiness check | **Architect** + PM | BMAD `bmad-check-implementation-readiness` | PASS/CONCERNS/FAIL |
| 2.5 | **Quality Gate 2** | User + Architect | Architecture review checklist | ✅ Proceed / ❌ Revise |

**Agent Collaboration:**
- Architect designs with BMAD structure
- Oracle (read-only GPT-5.2 equivalent) provides second opinion on architecture decisions
- PM breaks requirements into stories that trace back to PRD

#### Phase 3: Implementation

**Goal:** Build it, one story at a time, with discipline.

| Step | Activity | Agent/Role | Technique | Output |
|------|----------|------------|-----------|--------|
| 3.1 | Pick next story | **Developer** (Sisyphus) | Todo-driven workflow | Selected story |
| 3.2 | Think before coding | **Developer** | Karpathy: baby steps, plan first | Mental model |
| 3.3 | Write tests FIRST | **Developer** | `/tdd` skill | Failing tests |
| 3.4 | Implement minimum to pass | **Developer** | Karpathy: simplest code that works | Passing tests |
| 3.5 | Refactor if needed | **Developer** | Karpathy: no premature abstraction | Clean code |
| 3.6 | Verify everything | **Developer** | Karpathy: assert, print, check | Verified implementation |
| 3.7 | Parallel research (if stuck) | **Librarian/Explore** (background) | OMO background agents | Research results |
| 3.8 | Consult Oracle (if blocked) | **Oracle** (on-demand) | OMO Oracle consultation | Strategic advice |
| 3.9 | Mark story done, pick next | **Developer** | Todo enforcer | Story completion |
| 3.10 | **Quality Gate 3** | Reviewer | All stories done + tests pass | ✅ Proceed / ❌ Fix |

**Agent Collaboration:**
- Developer (Sisyphus) is the primary executor, todo-driven
- Background Librarian/Explore agents handle research in parallel
- Oracle consulted when stuck on architecture/logic
- Frontend tasks delegated to specialized visual agents
- Sisyphus-Junior handles category-specific subtasks

#### Phase 4: Review & Deployment

**Goal:** Review, document, deploy.

| Step | Activity | Agent/Role | Technique | Output |
|------|----------|------------|-----------|--------|
| 4.1 | Code review | **Reviewer** (Oracle) | Code review skill | Review report |
| 4.2 | Fix review issues | **Developer** | Surgical fixes | Updated code |
| 4.3 | Write documentation | **Tech Writer** | BMAD `bmad-tech-writer` | User docs, API docs |
| 4.4 | Build & deploy | **DevOps** | CI/CD pipeline | Deployment |
| 4.5 | Smoke tests | **DevOps** | Automated smoke suite | Test results |
| 4.6 | **Quality Gate 4** | User + DevOps | Smoke tests pass | ✅ Ship / ❌ Rollback |

---

## 3. Implementation Guide

### 3.1 File Structure

```
project/
├── AGENTS.md                    # Agent operating guide (copy from Section 6)
├── CLAUDE.md                    # Claude Code specific config (optional)
├── CONTEXT.md                   # Shared project language (from /grill-with-docs)
├── .skills/                     # Skills directory
│   ├── grill-me.md              # Intent alignment skill
│   ├── grill-with-docs.md       # Alignment + shared language + ADRs
│   ├── tdd.md                   # Test-driven development
│   ├── diagnose.md              # Systematic debugging
│   ├── prototype.md             # Quick prototyping
│   ├── zoom-out.md              # Step back and reassess
│   ├── write-a-skill.md         # Create new skills
│   └── karpathy-principles.md   # Coding principles (baby steps, simplicity, etc.)
├── docs/
│   ├── product-brief.md         # Phase 0 output
│   ├── PRD.md                   # Phase 1 output
│   ├── ux-spec.md               # Phase 1 output (if applicable)
│   ├── architecture.md          # Phase 2 output
│   ├── ADRs/                    # Architecture Decision Records
│   │   ├── 001-database-choice.md
│   │   └── 002-auth-strategy.md
│   ├── epics/                   # Phase 2 output
│   │   ├── epic-1-auth.md
│   │   └── epic-2-dashboard.md
│   └── reviews/                 # Phase 4 output
│       └── code-review-001.md
├── src/                         # Source code
├── tests/                       # Test code
└── .github/                     # CI/CD, issue templates
```

### 3.2 Agent Roles, Responsibilities & Handoff Rules

| Role | Skill ID | Responsibilities | Inputs | Outputs | Handoff To |
|------|----------|-----------------|--------|---------|------------|
| **Analyst** | `analyst` | Discovery, research, product brief | User conversation | `product-brief.md`, research reports | PM |
| **PM** | `pm` | PRD creation, story definition, scope management | `product-brief.md` | `PRD.md`, epic/story files | Architect |
| **Architect** | `architect` | System design, ADRs, tech stack | `PRD.md`, `ux-spec.md` | `architecture.md`, ADRs | Developer |
| **UX Designer** | `ux-designer` | User experience, wireframes, design tokens | `PRD.md` | `ux-spec.md` | Architect |
| **Developer** | `dev` | Code, tests, refactoring (Sisyphus) | Stories, `architecture.md` | Source code, tests | Reviewer |
| **Reviewer** | `reviewer` | Code review, quality gates | Source code | Review report | Developer (fixes) or DevOps |
| **Tech Writer** | `tech-writer` | Documentation, standards | Source code, architecture | User/API docs | DevOps |
| **Oracle** | `oracle` | Read-only consultation (architecture, debugging) | Any question | Advice, analysis | No handoff (consultation only) |
| **Librarian** | `librarian` | Background research, docs search | Research query | Research results | No handoff (background only) |
| **DevOps** | `devops` | Build, deploy, CI/CD, infrastructure | Source code | Deployment, CI config | — |

**Handoff Rules:**
1. **Documents are contracts.** Each phase produces an artifact that becomes the next phase's input.
2. **No phase-skipping.** Implementation cannot start until Implementation Readiness passes.
3. **Bounded autonomy.** Developers must follow architecture; they cannot redesign it unilaterally.
4. **Oracle is read-only.** Oracle advises but never writes code or makes architecture decisions.
5. **Background agents are research-only.** Librarian/Explore cannot modify files.

### 3.3 Step-by-Step Implementation

#### Step 1: Install Skills

```bash
# Install Matt Pocock skills
npx skills@latest add mattpocock/skills

# Install Karpathy skills (manual — copy skill files to .skills/)
# Copy from https://github.com/forrestchang/andrej-karpathy-skills

# Install BMAD skills
npx skills@latest add bmad-code-org/BMAD-METHOD

# Install Oh-My-OpenCode (if using OpenCode runtime)
npx oh-my-opencode@latest
```

#### Step 2: Create AGENTS.md

Copy the "AI Agent Operating Workflow" (Section 6) into your project root as `AGENTS.md`. This file is auto-injected into agent context on every session.

#### Step 3: Run Setup

In your coding agent, run:
```
/setup-matt-pocock-skills
```
This configures issue tracker, triage labels, and doc location.

#### Step 4: Start Phase 0

```
/grill-with-docs I want to build [describe your project]
```
The agent will interview you, build shared vocabulary (CONTEXT.md), and create a product brief.

#### Step 5: Progress Through Phases

Follow the lifecycle in Section 2.2, using quality gates at each phase boundary. Use:
- BMAD workflows for structured output (`bmad-create-prd`, `bmad-create-architecture`)
- Matt Pocock skills for alignment and quality (`/grill-me`, `/tdd`, `/diagnose`)
- Karpathy principles during implementation (baby steps, verify everything)
- OMO orchestration for parallel execution (background agents, Oracle consultation)

---

## 4. Requirements & Installation

### 4.1 Tools & Dependencies

| Category | Tool | Purpose | Required? |
|----------|------|---------|-----------|
| **Coding Agent** | Claude Code, Cursor, Cline, or OpenCode | Primary AI coding interface | Yes (at least one) |
| **Agent Plugin** | Oh-My-OpenCode (`oh-my-opencode`) | Multi-model orchestration | Optional (for OMO features) |
| **Skill Installer** | `skills` CLI (`npx skills@latest`) | Install/manage skill files | Recommended |
| **Runtime** | Node.js 20+, npm/npx | Run skill installer | Yes (for skills CLI) |
| **Runtime** | Bun 1.1+ | Build/run Oh-My-OpenCode | If using OMO |
| **Version Control** | Git + GitHub/GitLab | Source control, issue tracking | Yes |
| **Issue Tracker** | GitHub Issues, Linear, or local files | Task management | Yes |
| **LSP Server** | TypeScript LS, Pyright, etc. | LSP refactoring tools | Recommended |
| **AST-Grep** | `ast-grep` CLI | AST-aware code search/transform | Recommended |

### 4.2 Model/Provider Requirements

| Agent Role | Recommended Model | Provider | Notes |
|------------|------------------|----------|-------|
| Orchestrator (Sisyphus) | Claude Opus 4.5 | Anthropic | Best for planning + execution |
| Oracle (Consultation) | GPT-5.2 | OpenAI | Best for logical reasoning |
| Librarian (Research) | Claude Sonnet 4.5 / Big Pickle | Any | Cost-efficient for search |
| Explore (Fast Grep) | GPT-5 Nano / Grok Code | Any | Cheapest, fastest |
| Visual Agent | Gemini 3 Flash | Google | Multimodal + frontend |
| Plan Review (Momus) | Claude Sonnet 4.5 | Anthropic | Cost-efficient validation |
| Code Generation | Any strong coder | Any | Follows Karpathy principles |

### 4.3 Configuration Files

| File | Location | Purpose |
|------|----------|---------|
| `AGENTS.md` | Project root | Agent operating guide (auto-injected) |
| `CLAUDE.md` | Project root | Claude Code specific instructions |
| `CONTEXT.md` | Project root | Shared project language/vocabulary |
| `oh-my-opencode.json` | `.opencode/` or `~/.config/opencode/` | OMO agent/model/hook config |
| `opencode.json` | `.opencode/` | OpenCode base config |
| `.skills/` | Project root | Skill markdown files |
| `docs/` | Project root | Phase output documents |

### 4.4 Security Considerations

| Concern | Mitigation |
|---------|-----------|
| **Secret management** | Never commit API keys. Use `.env` files (gitignored) or secret manager. Agent instructions must never include raw secrets. |
| **Environment isolation** | Use separate API keys per project. Rate-limit per key. Monitor token spend per agent. |
| **Code injection** | Agent output must be reviewed before merge. Use PR-based workflows. Never auto-deploy without human gate. |
| **Model data leakage** | Don't send proprietary code to models you don't trust. Use self-hosted models for sensitive code. |
| **Dependency injection** | Agents must not add dependencies without human approval. Enforce via rules in AGENTS.md. |
| **Version control** | All agent changes go through PRs. Require review before merge. Use `git-master` skill for atomic commits. |

---

## 5. Use Cases

### 5.1 Building a New Application from PRD

```
Phase 0: /grill-with-docs "Build a task management app for remote teams"
  → CONTEXT.md + product-brief.md

Phase 1: bmad-create-prd → PRD.md
  bmad-create-ux-design → ux-spec.md

Phase 2: bmad-create-architecture → architecture.md + ADRs
  bmad-create-epics-and-stories → Epic files
  bmad-check-implementation-readiness → PASS

Phase 3: For each story:
  /tdd → write test → implement → verify → refactor
  Fire background Librarian for API docs
  Consult Oracle for architecture questions

Phase 4: Code review → Documentation → Deploy
```

### 5.2 Refactoring an Existing Codebase

```
1. /zoom-out — assess current architecture
2. /improve-codebase-architecture — deepen modules, redesign interfaces
3. Create architecture.md documenting new structure + ADRs
4. For each module:
   /tdd → write characterization tests → refactor → verify
5. Code review → Documentation update
```

### 5.3 Debugging Production Issues

```
1. /diagnose — systematic debugging skill
   - Karpathy: print debug, assert shapes, verify everything
   - Remove randomness (set seeds)
2. Fire background Explore for fast codebase grep
3. Consult Oracle for strategic debugging advice
4. Fix with baby steps — one change, verify, repeat
5. Add regression test (TDD)
6. Code review → Deploy fix
```

### 5.4 Creating Reusable AI Coding Skills

```
1. /write-a-skill — create new skill
2. Skill structure:
   ---
   name: my-skill
   version: 1.0
   triggers: [when to apply]
   ---
   # Skill Name
   ## Principle
   ## When to Apply
   ## Workflow (numbered steps)
   ## Examples (DO / DON'T)
   ## Anti-patterns
3. Test skill on 3+ scenarios before sharing
4. Add to .skills/ directory, reference from other skills
```

### 5.5 Multi-Agent Software Delivery

```
1. Orchestrator (Sisyphus) picks up task
2. Fires background Librarian for docs/API research
3. Fires background Explore for codebase mapping
4. Delegates frontend stories to visual-engineering category (Sisyphus-Junior + Gemini)
5. Handles backend stories personally (Claude Opus)
6. Consults Oracle when stuck on architecture
7. Todo enforcer prevents quitting halfway
8. Comment checker prevents AI slop
9. git-master creates atomic commits per story
```

### 5.6 Documentation-Driven Development

```
1. /grill-with-docs → build CONTEXT.md + ADRs while planning
2. Write PRD as living documentation
3. Architecture.md as single source of truth
4. For each story: write doc comments BEFORE code (Karpathy: explain WHY)
5. Tech Writer agent generates user docs from code + architecture
6. Validate docs with bmad-tech-writer validate-doc
```

### 5.7 Test-Driven AI Coding Workflow

```
1. /tdd skill activates RED-GREEN-REFACTOR cycle
2. RED: Write failing test first
3. Agent implements minimum code to pass (Karpathy: simplest code that works)
4. GREEN: Test passes
5. REFACTOR: Clean up, but NO premature abstraction (Karpathy: wait for 3+ repetitions)
6. Verify: Run full suite, assert everything (Karpathy: verify everything)
7. Commit with git-master (atomic commit)
8. Next story
```

---

## 6. AI Agent Operating Workflow

> **This section can be copied directly into `AGENTS.md` or `CLAUDE.md`.**
> It is a self-contained operating guide for an AI coding agent.

---

```markdown
# AI Agent Operating Workflow

## Identity
You are an AI-driven software development agent operating within a structured, phase-gated workflow. You combine the discipline of BMAD methodology, the alignment techniques of Matt Pocock's skills, the coding principles of Andrej Karpathy, and the orchestration power of multi-model agent systems.

## Decision Rules

### Before Every Task
1. **CHECK SKILLS FIRST.** If a skill matches the task, invoke it immediately.
2. **PHASE GATE.** Determine which project phase you are in. You cannot skip phases.
3. **THINK BEFORE CODE.** Never start coding without understanding the full scope. Use grilling if intent is unclear.

### Phase Determination
| If... | You are in Phase... | Your next action |
|-------|---------------------|-----------------|
| No product brief or CONTEXT.md exists | Phase 0: Discovery | Run grilling session |
| PRD does not exist | Phase 1: Planning | Create PRD from brief |
| No architecture doc | Phase 2: Solutioning | Create architecture from PRD |
| Architecture approved, stories defined | Phase 3: Implementation | Pick next story, TDD cycle |
| All stories implemented | Phase 4: Review & Deploy | Code review, deploy |

### Model Selection (when multi-model available)
| Task Type | Use Model | Reason |
|-----------|-----------|--------|
| Planning, orchestration | Strongest reasoning model | Complex decision-making |
| Code review, debugging | High-IQ consultation model | Logical analysis |
| Fast grep, exploration | Cheapest fast model | Low-cost, high-speed |
| Frontend, visual | Multimodal model | Visual understanding |
| Background research | Cost-efficient model | Parallel, non-blocking |

## Execution Steps

### Phase 0: Discovery
1. Run grilling session — ask user detailed questions about what they want
2. Build CONTEXT.md — create shared vocabulary for the project
3. Research domain (fire background agents if available)
4. Produce `product-brief.md`
5. **Gate:** User approves brief → proceed to Phase 1

### Phase 1: Planning
1. Create `PRD.md` from product brief — include user stories, acceptance criteria, NFRs
2. If UI involved: create `ux-spec.md`
3. **Gate:** PRD passes completeness check → proceed to Phase 2

### Phase 2: Solutioning
1. Create `architecture.md` — tech stack, component boundaries, data models, API contracts
2. Write ADRs for major decisions
3. Create epics and stories that trace back to PRD requirements
4. Run implementation readiness check
5. **Gate:** Architecture review passes → proceed to Phase 3

### Phase 3: Implementation
For each story:
1. Read story requirements + relevant architecture section
2. **RED:** Write failing test(s) first
3. **GREEN:** Implement simplest code that passes tests
4. **VERIFY:** Run tests, assert outputs, check for regressions
5. **REFACTOR:** Clean up only if pattern repeats 3+ times (no premature abstraction)
6. If stuck: consult Oracle (read-only architecture advice)
7. If need research: fire background Librarian/Explore
8. Commit with atomic commit message referencing story ID
9. Mark story done, pick next

**Gate:** All stories complete + all tests pass → proceed to Phase 4

### Phase 4: Review & Deploy
1. Run code review — check for bugs, security issues, adherence to architecture
2. Fix review issues with surgical changes (baby steps)
3. Generate documentation
4. Build and deploy
5. Run smoke tests
6. **Gate:** Smoke tests pass → SHIP

## Quality Gates

### Gate 0: Brief Approved
- [ ] Product brief captures strategic vision
- [ ] CONTEXT.md defines shared language
- [ ] Scope is bounded (what's IN and OUT)

### Gate 1: PRD Approved
- [ ] All user stories have acceptance criteria
- [ ] Non-functional requirements specified
- [ ] No ambiguous requirements (all terms defined in CONTEXT.md)

### Gate 2: Architecture Approved
- [ ] Tech stack justified with ADRs
- [ ] Component boundaries defined
- [ ] Data models and API contracts documented
- [ ] Implementation readiness: PASS

### Gate 3: Implementation Complete
- [ ] All stories implemented
- [ ] All tests pass (unit + integration)
- [ ] No failing tests, no skipped tests
- [ ] Code follows architecture (no unauthorized design changes)

### Gate 4: Ready to Ship
- [ ] Code review passed
- [ ] Documentation updated
- [ ] Smoke tests pass in target environment
- [ ] No critical/high bugs open

## Review Checklist (for Code Review)

### Correctness
- [ ] Code does what the story requires
- [ ] Edge cases handled
- [ ] Error handling is robust

### Quality
- [ ] Tests cover happy path + error cases
- [ ] No dead code or commented-out blocks
- [ ] No AI slop comments (comments explain WHY, not WHAT)
- [ ] Naming is consistent with CONTEXT.md vocabulary

### Security
- [ ] No hardcoded secrets
- [ ] Input validation on all user inputs
- [ ] Dependencies are approved (not added without permission)

### Architecture Adherence
- [ ] Code follows architecture.md structure
- [ ] No unauthorized cross-boundary calls
- [ ] ADRs are respected

### Simplicity (Karpathy Principles)
- [ ] Code is the simplest that could work
- [ ] No premature abstraction (pattern appears < 3 times? Don't abstract)
- [ ] No clever one-liners where straightforward code would do
- [ ] Functions do one thing clearly

## Escalation Rules

| Situation | Action |
|-----------|--------|
| Intent is unclear | STOP. Run grilling session. Do not guess. |
| Architecture doesn't cover the case | STOP. Consult Oracle. Escalate to user if Oracle is uncertain. |
| Stuck in a loop (3+ failed attempts) | STOP. Consult Oracle or fire background research. Do not keep trying the same approach. |
| Test keeps failing after 3 fixes | STOP. Zoom out. Re-read the story. Maybe the approach is wrong. |
| Scope creep detected | STOP. Flag to user. Do not implement beyond story requirements. |
| Dependency addition needed | ASK user. Never add dependencies without approval. |
| Cross-boundary code change needed | ASK user. Do not break architecture boundaries unilaterally. |
| Quality gate fails | FIX issues. Do not proceed to next phase until gate passes. |

## Coding Principles (Karpathy)

1. **Baby Steps:** Make one small change, verify it works, repeat. Never write 50 lines without testing.
2. **Simplicity First:** Write the simplest code that could possibly work. Prefer flat code over hierarchies.
3. **No Premature Abstraction:** Don't create abstract classes, interfaces, or design patterns until you see the same pattern 3+ times.
4. **Verify Everything:** Use assertions, print debugging, and shape checks. Never assume — verify.
5. **Readability Over Cleverness:** Write code for humans. Obvious > elegant. Descriptive names > short names.
6. **Always Be Running:** Keep code in a runnable state. If it breaks, fix it before adding new features.
7. **Explain WHY, Not WHAT:** Comments should explain reasoning, not describe syntax.
8. **Surgical Changes:** Make minimal, precise changes. Don't refactor unrelated code.

## Anti-Patterns (MUST AVOID)

- Adding dependencies without user approval
- Creating abstract classes for single implementations
- Writing "clever" code that needs explanation
- Skipping tests to "save time"
- Modifying files outside the story's scope
- Adding excessive comments (AI slop)
- Guessing requirements instead of asking
- Proceeding past a failing quality gate
- Auto-deploying without human review
- Deleting failing tests instead of fixing code
```

---

*Document generated by RESA Research Agent. Sources: GitHub repositories analyzed via GitHub API and raw content extraction. Framework details for mattpocock/skills, BMAD-METHOD, and oh-my-openagent verified against live repository data. Karpathy skills analysis based on established knowledge of Karpathy's coding philosophy and the repository's skill-format encoding.*
