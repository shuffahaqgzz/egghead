---
title: Memory and RAG Architecture
created: 2026-06-20
updated: 2026-06-20
type: concept
tags:
  - memory
  - rag
  - honcho
  - knowledge
  - retrieval
sources:
  - HONCHO_MEMORY_INTEGRATION.md
  - RAG_ARCHITECTURE_PLAN.md
---

# Memory and RAG Architecture

Memory layer integration (Honcho) and RAG architecture for the KUMACHII-STELLA multi-agent system. Provides persistent memory across sessions, platforms, and profiles with cross-agent shared context.

## Honcho Memory Integration

### Status
- **Cost:** $0/month (fully self-hosted, GPU-accelerated)
- **Endpoint:** `http://10.50.0.111:8500` (LAN-only)
- **Workspace:** `hermes` (single workspace, all 11 profiles share)

### Architecture
```text
                       ┌─────────────────────────────┐
                       │  10.50.0.111:8500           │
                       │  Honcho API (FastAPI)       │
                       │  + deriver worker × 4       │
                       └──────────┬──────────────────┘
                                  │
              ┌───────────────────┼───────────────────┐
              ▼                   ▼                   ▼
     ┌─────────────────┐  ┌──────────────┐  ┌────────────────┐
     │ honcho-postgres │  │ honcho-redis │  │ honcho-ollama  │
     │ pgvector:pg15   │  │ 8.2-alpine   │  │ ollama:0.6.6   │
     │ docs+peers+sess │  │ cache + queue│  │ qwen2.5:7b     │
     │                 │  │              │  │ + nomic-embed  │
     │  768-dim HNSW   │  │              │  │  GPU resident  │
     └─────────────────┘  └──────────────┘  └────────────────┘
                                                    │
                                                    ▼
                                          ┌─────────────────┐
                                          │  RTX A5000 GPU  │
                                          │  23GB VRAM      │
                                          │  ~7GB used      │
                                          └─────────────────┘
```

### Stack
| Layer | Implementation | Why |
|---|---|---|
| API server | Honcho v3 (FastAPI) | Official, supports parse() with Pydantic strict json_schema |
| Database | postgres 15 + pgvector | Required by Honcho. HNSW index for ANN search |
| Cache/queue | redis 8.2 | Queue worker uses Redis for claim coordination |
| LLM inference | Ollama 0.6.6 + qwen2.5:7b-instruct | Free, GPU-accelerated, supports strict json_schema |
| Embedding | Ollama + nomic-embed-text (768 dim) | Free, fast, matches schema |
| GPU | RTX A5000 23GB | Hosts both models simultaneously |

### Peer Identity Model
| Peer | Role |
|---|---|
| `gilang` | Canonical user identity (all platforms unified via `pinPeerName: true`) |
| `hermes` | Default profile's AI peer |
| `kumachii` / `stella` / etc. | One AI peer per specialist agent |

### Observation Strategy
| Profile | user.observeMe | user.observeOthers | ai.observeMe | ai.observeOthers |
|---|:---:|:---:|:---:|:---:|
| default | T | T | T | F |
| kumachii | T | T | T | **T** |
| stella | T | T | T | **T** |
| edison | T | T | T | F |
| (other specialists) | T | T | T | F |

### Recall Patterns
**Pattern 1 — Per-agent private view:**
```bash
POST /v3/workspaces/hermes/peers/<AGENT>/chat
{"target": "gilang", "query": "..."}
```
Returns ONLY observations where `observer=<AGENT>` AND `observed=gilang`.

**Pattern 2 — Globally merged view:**
```bash
POST /v3/workspaces/hermes/peers/gilang/chat
{"query": "..."}
```
Returns observations across ALL observers.

### Verification Results
- Cross-agent observation derivation: 32 facts derived from 10 agents in ~25s
- Cross-agent privacy isolation: Per-(observer,observed) isolation works as designed
- Identity unification: 308 docs migrated to canonical `gilang` peer

## RAG Architecture

### Source-of-Truth Hierarchy
| Level | Source Type | Owner |
|---|---|---|
| 1 | Source-of-truth documents | STELLA |
| 2 | Architecture + ADRs | SHAKA |
| 3 | PRD + acceptance criteria | STELLA |
| 4 | Code + tests | EDISON |
| 5 | Deployment + runbook docs | LILITH + STUSSY |
| 6 | Research reports | PYTHAGORAS |
| 7 | Generated summaries | Any agent |
| 8 | Chat history | System |

**Rule:** Generated summaries (level 7-8) must NEVER override primary sources (level 1-6).

### RAG Pipeline
```text
Ingest → Clean → Chunk → Tag → Embed → Index → Retrieve → Rerank → Answer → Cite → Validate
```

### Chunk Metadata Schema
```yaml
source_file: "docs/architecture.md"
source_type: "architecture"
owner_agent: "SHAKA"
created_at: "2026-05-10T14:00:00Z"
project: "finance-dashboard"
phase: "2"
freshness: "fresh"
authority_level: 2
contains_secret: false
```

### Non-Negotiable Rules
1. Never ingest secrets or raw credentials
2. Mark stale content explicitly (30 days → stale, 90 days → deprecated)
3. Cite source on every retrieval
4. Keep chunk size consistent (256-768 tokens)
5. Retrieval tests for important docs
6. Re-index after major doc changes
7. Source-of-truth preserved
8. Per-project namespace
9. BONNEY is sole curator

### Storage Decision
| Option | Pros | Cons | Best for |
|---|---|---|---|
| RAG-1: Vector DB | Semantic search | Needs embedding model | Large KB |
| RAG-2: BM25 file-based | Fast, no ML, simple | Keyword-only | Small KB |
| RAG-3: Hybrid | Best quality | Most complex | Production-grade |

**Recommendation:** Start with RAG-2 (BM25), upgrade to RAG-3 when KB > 100 docs.

## Related Pages

- [[multi-agent-workflow-framework]] - Framework specification
- [[deployment-and-operations]] - Memory layer deployment status
- [[skills-architecture]] - BONNEY skills for RAG pipeline
