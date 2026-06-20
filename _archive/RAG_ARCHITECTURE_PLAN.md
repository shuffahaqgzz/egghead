# RAG Architecture Plan — BONNEY Knowledge Pipeline

**Version:** 1.0.0
**Date:** 2026-05-13
**Owner:** BONNEY (after Stage D online), interim: Hermes default profile
**Phase:** 5.0 Conceptual Fix — step 6/9
**Framework ref:** Section 16 (RAG Architecture for BONNEY)

---

## 0. Purpose

Dokumen ini mendefinisikan arsitektur RAG (Retrieval-Augmented Generation) untuk multi-agent system. BONNEY adalah owner pipeline ini. Semua agent bisa **query** knowledge base, tapi hanya BONNEY yang **ingest/curate**.

RAG bukan optional — ini Layer 5 (Token Optimization) requirement. Tanpa RAG, agent akan dump full docs ke context → token waste → quality drop.

---

## 1. Source-of-Truth Hierarchy

Authority level 1 (highest) → 8 (lowest). Saat retrieval conflict, higher authority wins.

| Level | Source Type | Example | Owner |
|---|---|---|---|
| 1 | Source-of-truth documents | `CONTEXT.md`, signed-off specs | STELLA |
| 2 | Architecture + ADRs | `docs/architecture.md`, `docs/ADRs/*.md` | SHAKA |
| 3 | PRD + acceptance criteria | `docs/PRD.md`, `docs/epics/*.md` | STELLA |
| 4 | Code + tests | `src/**`, `tests/**` | EDISON |
| 5 | Deployment + runbook docs | `docs/deployment.md`, `docs/runbook.md` | LILITH + STUSSY |
| 6 | Research reports | `docs/research/*.md` | PYTHAGORAS |
| 7 | Generated summaries | Agent-produced summaries, session compress | Any agent |
| 8 | Chat history | Session transcripts | System |

**Rule:** Generated summaries (level 7-8) must NEVER override primary sources (level 1-6). If conflict detected, flag to BONNEY for resolution.

---

## 2. RAG Pipeline

```
┌─────────┐    ┌───────┐    ┌───────┐    ┌─────┐    ┌───────┐    ┌───────┐
│ Ingest  │ →  │ Clean │ →  │ Chunk │ →  │ Tag │ →  │ Embed │ →  │ Index │
└─────────┘    └───────┘    └───────┘    └─────┘    └───────┘    └───────┘
                                                                       │
┌──────────┐    ┌──────┐    ┌────────┐    ┌────────┐    ┌──────────┐  │
│ Validate │ ←  │ Cite │ ←  │ Answer │ ←  │ Rerank │ ←  │ Retrieve │←─┘
└──────────┘    └──────┘    └────────┘    └────────┘    └──────────┘
```

### 2.1 Ingest

- Trigger: new/updated doc committed to project, manual BONNEY ingest command, post-phase gate PASS
- Source: `docs/`, `~/.hermes/knowledge/`, project `src/` (selective), external research
- Filter: skip binary, skip secrets (`.env`, `auth.json`, private keys)
- Output: raw text per document

### 2.2 Clean

- Strip formatting noise (excessive whitespace, HTML artifacts)
- Normalize markdown headers
- Remove duplicate content (copy-paste between docs)
- Preserve code blocks intact

### 2.3 Chunk

- Strategy: **semantic chunking** (by section/header) preferred over fixed-size
- Fallback: 512-token chunks with 64-token overlap for docs without clear structure
- Code files: chunk by function/class boundary
- Keep chunk self-contained (include parent header context)

### 2.4 Tag (Metadata)

Every chunk gets mandatory metadata (see Section 3 below).

### 2.5 Embed

- **Phase 5.3 decision** — storage backend TBD:
  - Option RAG-1: Local vector DB (chroma / qdrant / lancedb)
  - Option RAG-2: File-based BM25 (tantivy / ripgrep keyword search)
  - Option RAG-3: Hybrid (BM25 + embedding rerank)
- Embedding model: `nomic-embed-text` or `bge-base-en-v1.5` (local, free)
- Fallback: OpenAI `text-embedding-3-small` (paid, higher quality)

### 2.6 Index

- Index per-project (namespace isolation)
- Cross-project shared KB indexed separately
- Re-index trigger: major doc change, post-deploy, weekly scheduled

### 2.7 Retrieve

- Query from any agent (EDISON needs code context, PYTHAGORAS needs research, SHAKA needs ADRs)
- Top-K: 5-10 chunks default
- Filter by: project, phase, authority_level, source_type, freshness

### 2.8 Rerank

- Rerank retrieved chunks by relevance to query
- Boost higher authority_level chunks
- Penalize stale chunks (freshness = "stale" or "deprecated")
- Model: cross-encoder reranker (local) or LLM-based rerank

### 2.9 Answer

- Agent uses retrieved chunks as context for response
- Must cite source (file path + authority level)

### 2.10 Cite

- Every answer that uses RAG must include citation:
  ```
  [Source: docs/architecture.md (authority: 2, updated: 2026-05-10)]
  ```

### 2.11 Validate

- Periodic retrieval tests (see Section 5)
- Freshness audit (monthly)
- Authority conflict detection

---

## 3. Chunk Metadata Schema (Mandatory)

```yaml
source_file: "docs/architecture.md"
source_type: "architecture"          # prd | architecture | adr | code | runbook | research | summary | chat | ops
owner_agent: "SHAKA"                 # which agent owns this doc
created_at: "2026-05-10T14:00:00Z"
updated_at: "2026-05-13T07:00:00Z"
project: "finance-dashboard"         # project namespace
phase: "2"                           # 0-6, which phase produced this
version: "1.0"
freshness: "fresh"                   # fresh | stale | deprecated
authority_level: 2                   # 1-8 per hierarchy
contains_secret: false               # MUST be false, secrets never ingested
chunk_id: "arch-001-components"      # unique within project
parent_doc_title: "System Architecture"
section_path: "## 3. Component Boundaries"  # header breadcrumb
```

**Validation rules:**
- `contains_secret: true` → reject chunk, alert BONNEY
- `authority_level` must match source_type (architecture = 2, not 7)
- `freshness: deprecated` → exclude from default retrieval (only include if explicitly requested)

---

## 4. Non-Negotiable Rules

1. **Never ingest secrets** — scan for patterns (API keys, tokens, passwords) before ingest. Reject + alert.
2. **Never ingest raw credentials** — `.env`, `auth.json`, private keys are blocklisted.
3. **Mark stale content explicitly** — docs not updated in 30 days get `freshness: stale`. 90 days → `deprecated`.
4. **Cite source on every retrieval** — no anonymous knowledge injection.
5. **Keep chunk size consistent** — 256-768 tokens target range.
6. **Retrieval tests for important docs** — every source-of-truth doc (level 1-3) must have at least 1 retrieval test.
7. **Re-index after major doc changes** — post-Gate 2 (architecture), post-Gate 4 (review), post-Gate 5 (deploy).
8. **Source-of-truth preserved** — generated summaries never overwrite primary docs.
9. **Per-project namespace** — no cross-project chunk pollution.
10. **BONNEY is sole curator** — other agents can request ingest, only BONNEY executes.

---

## 5. Retrieval Test Template

For each critical document, maintain a retrieval test:

```markdown
# RAG Retrieval Test

## Test ID: RT-001
## Document: docs/architecture.md
## Section: Component Boundaries

Question: "What are the main components of the finance dashboard?"

Expected source: docs/architecture.md (authority: 2)

Expected answer points:
- [ ] Lists all major components
- [ ] Mentions data flow between them
- [ ] References storage model

Retrieved chunks:
- (filled after test run)

Relevance score: (filled after test run)

Pass/Fail: (filled after test run)

Notes:
- (any issues with retrieval quality)
```

**Test cadence:**
- After every re-index: run all retrieval tests
- Weekly: run random sample (20% of tests)
- After Gate 2 PASS: run architecture-related tests
- After Gate 5 PASS: run deployment/runbook tests

**Pass criteria:** Expected source appears in top-3 retrieved chunks, all expected answer points addressable from retrieved context.

---

## 6. Storage Decision (Deferred to Phase 5.3)

| Option | Pros | Cons | Best for |
|---|---|---|---|
| **RAG-1: Vector DB** (chroma/qdrant/lancedb) | Semantic search, good for natural language queries | Needs embedding model, more infra | Large KB, diverse queries |
| **RAG-2: BM25 file-based** (tantivy/ripgrep) | Fast, no ML model, simple, cheap | Keyword-only, misses semantic similarity | Small KB, exact-match queries |
| **RAG-3: Hybrid** (BM25 + vector rerank) | Best quality, handles both keyword and semantic | Most complex, needs both infra | Production-grade RAG |

**Recommendation:** Start with **RAG-2** (BM25 file-based) for Stage A-C. Upgrade to **RAG-3** (hybrid) when BONNEY goes live in Stage D and KB grows beyond ~100 docs.

**Rationale:** BM25 is zero-dependency (ripgrep already installed), works immediately, and for a KB of <100 docs the quality difference vs vector search is minimal. Upgrade path is additive (add embedding layer on top), not rewrite.

---

## 7. Knowledge Base Structure

```
~/.hermes/knowledge/                    # Shared KB (BONNEY curates)
├── global/
│   ├── USER_PROFILE.md                 # Authority: 1
│   ├── INFRASTRUCTURE.md              # Authority: 1
│   └── INTEGRATION_STATE.md           # Authority: 1
├── projects/
│   └── <project>/
│       ├── TECH_STACK.md              # Authority: 3
│       ├── ARCHITECTURE.md            # Authority: 2 (copy/link from docs/)
│       ├── ERROR_PATTERNS.md          # Authority: 5
│       ├── SETUP_GUIDE.md             # Authority: 5
│       └── DECISION_LOG.md            # Authority: 2
├── topics/
│   └── framework-v1.1.0/             # This migration docs
└── rag/
    ├── ingestion-manifest.yaml        # What's been ingested, when
    ├── retrieval-tests/               # RT-001.md, RT-002.md, ...
    ├── freshness-report.md            # Monthly freshness audit
    └── index/                         # BM25 index files (or vector DB data)
```

---

## 8. Agent Access Patterns

| Agent | Query KB? | Request ingest? | Direct write KB? |
|---|---|---|---|
| KUMACHII | Yes (personal + project) | Yes (via handoff to BONNEY) | No |
| STELLA | Yes (workflow state, decisions) | Yes | Limited (decision log only) |
| SHAKA | Yes (architecture, ADRs) | Yes | No (writes to `docs/`, BONNEY ingests) |
| EDISON | Yes (code context, patterns) | Yes | No |
| PYTHAGORAS | Yes (research, sources) | Yes | No (writes to `docs/research/`, BONNEY ingests) |
| ATLAS | Yes (security policies) | Yes | No |
| YORK | Yes (QA checklists, reviews) | Yes | No |
| LILITH | Yes (deployment refs, runbooks) | Yes | No |
| BONNEY | Yes + **curate + ingest + index** | N/A (self) | **Yes** (sole curator) |
| STUSSY | Yes (ops baselines, incidents) | Yes | No |

---

## 9. Freshness Management

| Age since last update | Status | Action |
|---|---|---|
| 0-30 days | `fresh` | Normal retrieval |
| 31-90 days | `stale` | Include in retrieval but flag to user: "[stale: last updated X days ago]" |
| 91+ days | `deprecated` | Exclude from default retrieval. Include only if explicitly requested. Flag for BONNEY review. |

**Freshness audit:** BONNEY runs monthly. Output: `~/.hermes/knowledge/rag/freshness-report.md`.

---

## 10. Implementation Phases

| Phase | What | Dependency |
|---|---|---|
| 5.1 (Stage A) | Shared KB structure exists, manual file-based retrieval (ripgrep) | None |
| 5.3 (Handoff infra) | BM25 index built, retrieval tests written for top-10 docs | Stage A stable |
| 5.4 (Stage B) | PYTHAGORAS + EDISON can query KB via skill | PYTHAGORAS + EDISON profiles live |
| 5.5 (Stage D) | BONNEY profile live, full pipeline automated, freshness audit scheduled | BONNEY profile created |
| Future | Upgrade to hybrid RAG-3 if KB > 100 docs | BONNEY operational |

---

## 11. References

- Framework Section 16: RAG Architecture for BONNEY
- Framework Section 16.1: Knowledge Source Hierarchy
- Framework Section 16.2: RAG Pipeline
- Framework Section 16.3: Metadata Required
- Framework Section 16.4: RAG Rules
- Framework Section 16.5: Retrieval Test Template
- BONNEY SOUL.md: `soul-drafts/BONNEY-SOUL.md`
- Phase 5.0 Plan: `PHASE_5_0_CONCEPTUAL_FIX_PLAN.md` Section 5

---

**Next step Phase 5.0:** Stage A Foundation Checklist.
