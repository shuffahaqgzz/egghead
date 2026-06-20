# Deep Research: opencode-hermes-multiagent

> **Repo:** https://github.com/1ilkhamov/opencode-hermes-multiagent
> **Date:** 2026-06-04
> **Method:** 3 parallel research agents (dispatching-parallel-agents skill)

---

## TL;DR

Multi-agent orchestration system untuk **OpenCode AI** (AI coding tool). 17 specialized subagents dalam 6 domain, di-orchestrate oleh 1 master agent ("Hermes") yang **tidak pernah menyentuh kode langsung** — hanya dispatch, coordinate, validate.

---

## 1. Architecture Overview

### Hub-and-Spoke Model

```
                    ┌──────────────┐
                    │   HERMES     │
                    │  (Master)    │
                    │ 3 tools only │
                    │ task/todo    │
                    └──────┬───────┘
                           │ dispatch
        ┌──────────────────┼──────────────────┐
        │                  │                  │
   ┌────▼────┐      ┌─────▼─────┐     ┌─────▼─────┐
   │Research │      │Planning   │     │Implement  │
   │ finder  │      │ architect │     │ coder     │
   │ analyst │      │ planner   │     │ editor    │
   │researcher│     └───────────┘     │ fixer     │
   └─────────┘                        │ refactorer│
        │                             └───────────┘
   ┌────▼────┐      ┌───────────┐     ┌───────────┐
   │Quality  │      │Infra      │     │Docs       │
   │ reviewer│      │ devops    │     │ documenter│
   │ tester  │      │ optimizer │     │ commenter │
   │ debugger│      └───────────┘     └───────────┘
   │ security│
   └─────────┘
```

### Core Principle: Least Privilege

- **Hermes (master):** Hanya `task`, `todowrite`, `todoread`. TIDAK bisa read/write/bash.
- **Research/Planning agents:** Read-only (read, list, glob, grep). Beberapa dapat `lsp` atau `webfetch`.
- **Implementation agents:** Full toolkit (bash, read, write, edit, lsp, dll).
- **Quality agents:** Reviewer/Security = read-only. Tester/Debugger = dapat bash.
- **Tidak ada subagent yang punya `task` tool** — mencegah runaway delegation.

---

## 2. Tech Stack

| Component | Detail |
|-----------|--------|
| Runtime | Bun (TypeScript, ESM) |
| Language | TypeScript 5.7+ |
| Core SDK | `@opencode-ai/plugin` v1.0.218 |
| Config | `opencode.json` (JSON + $schema) |
| Agent defs | Markdown + YAML frontmatter |
| MCP servers | Context7 (library docs), mcp-server-fetch |
| Auth | OpenAI Codex auth, Google Antigravity auth |
| Models | GPT 5.2 family (8 variants), Gemini 3 (Pro/Flash), Claude Sonnet/Opus 4.5 |

### Model-to-Agent Mapping (Cost-Optimized)

| Agent | Model | Reasoning |
|-------|-------|-----------|
| Hermes (orchestrator) | GPT 5.2 High | Strong routing reasoning |
| Coder, Editor | Claude Opus 4.5 Thinking High | Best code generation |
| Reviewer, Tester, Security | GPT 5.2 Codex Extra High | Max accuracy for quality gates |
| Finder, Planner | Gemini 3 Flash | Fast + cheap untuk search/decomposition |
| Researcher | Claude Sonnet 4.5 Thinking Low | Lightweight web lookups |
| Fixer, Refactorer, Debugger, Optimizer | Claude Sonnet 4.5 Thinking High | Constrained but capable |

---

## 3. Pipeline System (7 Predefined Pipelines)

| Pipeline | Sequence |
|----------|----------|
| **New Feature** | finder → analyst → architect → planner → coder → reviewer → tester → documenter |
| **Security Feature** | finder → analyst → researcher → architect → planner → coder → reviewer → security → tester → documenter |
| **Bug (unknown cause)** | finder → debugger → fixer → reviewer → tester |
| **Bug (known cause)** | finder → fixer → reviewer → tester |
| **Refactoring** | finder → analyst → refactorer → reviewer → tester |
| **Performance** | finder → analyst → optimizer → reviewer → tester |
| **Infrastructure** | finder → devops → reviewer → tester |

### Pipeline Invariants
- `@finder` **SELALU pertama** — tanpa exception
- `@reviewer` + `@tester` **WAJIB** setelah code change apapun
- `@security` **WAJIB** untuk auth/user data/secrets/payment
- `@security FAIL` = **HARD STOP** — pipeline berhenti total
- Revision loops: max 3 iterasi coder↔reviewer / fixer↔tester sebelum escalate ke user
- 4 checkpoint gates: after research, planning, implementation, quality (user approval required)

---

## 4. Complete Agent Inventory (17 Agents)

### Research (3)
| Agent | Role | Tools | Constraint |
|-------|------|-------|------------|
| **Finder** | Codebase scout | read, list, glob, grep | NO analysis, NO recommendations |
| **Analyst** | Deep code analyst (deps, flow, risks) | read, list, glob, grep, lsp | Read-only. No fix suggestions |
| **Researcher** | External knowledge (docs, web) | read, list, glob, grep, webfetch, MCP | Must cite sources |

### Planning (2)
| Agent | Role | Tools | Constraint |
|-------|------|-------|------------|
| **Architect** | Solution design | read, list, glob, grep | Interfaces only, no impl code |
| **Planner** | Task decomposition | read, list, glob, grep, todowrite/read | Max 5-15 tasks per feature |

### Implementation (4)
| Agent | Role | Tools | Constraint |
|-------|------|-------|------------|
| **Coder** | Create NEW files | bash, read, write, edit, list, glob, grep, lsp | Follow Architect design exactly |
| **Editor** | Modify EXISTING files | bash, read, write, edit, list, glob, grep, lsp | Must read entire file first |
| **Fixer** | Fix bugs (minimal changes) | bash, read, write, edit, list, glob, grep, lsp | Fix ONLY the bug, nothing else |
| **Refactorer** | Restructure, same behavior | bash, read, write, edit, list, glob, grep, lsp | Tests before AND after mandatory |

### Quality (4)
| Agent | Role | Tools | Constraint |
|-------|------|-------|------------|
| **Reviewer** | Code review gate | read, list, glob, grep, lsp | Read-only. Mandatory |
| **Security** | Security auditor (OWASP) | read, list, glob, grep, lsp | FAIL = pipeline STOPS |
| **Tester** | Test engineer | bash, read, write, edit, list, glob, grep, lsp | Full access. Mandatory |
| **Debugger** | Bug investigator | bash, read, list, glob, grep, lsp | Diagnose only, never fix |

### Infrastructure (2)
| Agent | Role | Tools | Constraint |
|-------|------|-------|------------|
| **DevOps** | CI/CD, Docker, K8s | bash, read, write, edit, list, glob, grep | Full access |
| **Optimizer** | Performance engineer | bash, read, write, edit, list, glob, grep, lsp | Measure before AND after |

### Documentation (2)
| Agent | Role | Tools | Constraint |
|-------|------|-------|------------|
| **Documenter** | API docs, README, guides | read, write, edit, list, glob, grep | Match existing style |
| **Commenter** | JSDoc, inline comments | read, edit, list, glob, grep, lsp | Explain WHY not WHAT |

---

## 5. Key Design Decisions

### 5.1 Markdown-as-Prompt
Agent definitions = `.md` files dengan YAML frontmatter. Body = system prompt. Human-readable, version-controllable.

### 5.2 Structured Response Protocol
Setiap agent WAJIB output structured response:
```
STATUS: PASS | FAIL | NEEDS_REVISION
RESULT: <summary>
<agent-specific fields>
```
Enables machine-parseable pipeline coordination.

### 5.3 Context Accumulation
Setiap agent menerima **full accumulated context** dari semua agent sebelumnya. Coder receives context paling besar (6 inputs: original request + 4 research/planning outputs + session learnings).

### 5.4 Session Learning
System tracks recurring issues within session, proactively warns downstream agents. Lightweight in-session feedback loop.

### 5.5 Dual Pathways
- **Feature pathway:** Research → Architect → Planner → Coder/Editor/Refactorer
- **Bug-fix pathway:** Debugger → Fixer (bypasses Architect/Planner entirely)

### 5.6 Privacy-First Model Config
All OpenAI models: `store: false` + encrypted reasoning content. Enterprise/private API endpoints.

---

## 6. Comparison: vs AI-Driven Multi-Agent Framework v1.1.0

### Direct Role Mapping

| Hermes Agent | v1.1.0 Agent | Overlap |
|---|---|---|
| Architect | SHAKA | HIGH |
| Coder | EDISON | HIGH (but EDISON = monolithic) |
| Researcher | PYTHAGORAS | HIGH (Hermes splits into 3) |
| Security | ATLAS | HIGH |
| Tester | YORK | HIGH |
| DevOps | LILITH | HIGH |
| Documenter | BONNEY | HIGH |
| Planner | STELLA (partial) | MEDIUM |
| — | KUMACHII (intake) | MISSING in Hermes |
| — | STUSSY (monitoring) | MISSING in Hermes |
| Finder, Analyst | — | MISSING in v1.1.0 |
| Debugger | — | MISSING in v1.1.0 |
| Reviewer | — | MISSING in v1.1.0 |
| Optimizer | — | MISSING in v1.1.0 |
| Editor, Fixer, Refactorer | — | MISSING in v1.1.0 |
| Commenter | — | MISSING in v1.1.0 |

### Architectural Comparison

| Dimension | Hermes (17 agents) | v1.1.0 (10 agents) |
|---|---|---|
| Specialization | Hyper-specialized | Generalist roles |
| Quality gates | Multi-gate (Reviewer → Security → Tester) | Single gate (YORK) |
| Model diversity | 5 tiers (flash → codex-xhigh) | Not specified |
| Tool restrictions | Fine-grained per-agent | Not specified |
| Code modification | 4 specialized agents | 1 agent (EDISON) |
| Research depth | 3-layer pipeline | 1 agent |
| Intake/validation | None | KUMACHII |
| Monitoring | None | STUSSY |
| Orchestration | Implicit (Hermes = master) | STELLA (dedicated) |
| Response format | Standardized structured output | Not specified |

### Integration Opportunities

| Opportunity | Value |
|---|---|
| Add KUMACHII intake → Hermes pipeline | Request validation before dispatch |
| Add STELLA orchestrator → replace Hermes master | Centralized routing logic |
| Add STUSSY monitoring → cross-cutting all agents | Runtime observability |
| Add Finder + Analyst → v1.1.0 | Deep codebase understanding |
| Add Reviewer → v1.1.0 | Quality gate between impl and test |
| Add Debugger → v1.1.0 | Separated diagnosis from fixing |
| Add Optimizer → v1.1.0 | Dedicated performance engineering |
| Split EDISON → 4 specialized agents | Better separation of concerns |

---

## 7. Assessment

### Strengths
- **Extreme specialization** — setiap agent punya narrow scope + tools yang tepat
- **Enforced quality gates** — tidak ada code yang ship tanpa review + test + security audit
- **Cost-optimized models** — cheap untuk simple tasks, expensive hanya untuk critical decisions
- **Structured protocol** — machine-parseable responses enable pipeline automation
- **Security as hard stop** — @security FAIL = pipeline mati, no bypass

### Weaknesses
- **No intake validation** — request langsung masuk pipeline tanpa sanity check
- **No runtime monitoring** — tidak ada agent yang observe system health
- **Complex pipeline** — 17 agents = coordination overhead untuk simple tasks
- **No persistent memory** — session learning only, resets between sessions
- **Single orchestrator** — Hermes master is SPOF, no redundancy

### Verdict
**Arsitektur solid untuk complex software engineering tasks.** Pipeline design + least privilege + structured responses = production-grade multi-agent system. Cocok untuk project yang butuh rigorous quality gates (enterprise, security-critical).

**Untuk v1.1.0:** Ambil Finder, Analyst, Reviewer, Debugger concepts. Keep KUMACHII + STUSSY yang Hermes tidak punya. Pertimbangkan split EDISON jadi specialized agents untuk complex projects.
