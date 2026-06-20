---
title: Token Optimization
created: 2026-06-20
updated: 2026-06-20
type: concept
tags:
  - token-optimization
  - rtk
  - caveman
  - code-review-graph
  - context
sources:
  - code-review-graph-vs-rtk-analysis.md
  - ai-development-workflow-analysis.md
---

# Token Optimization

Strategies and tools for reducing token usage in the multi-agent system, including RTK, Caveman, code-review-graph, context management, and RAG retrieval.

## Token Optimization Stack

### Layer 1: Smart Zone Discipline
- Split work into small tasks
- Use vertical slices
- Reset context between major phases
- Use separate sessions for review
- Do not load the whole codebase unless required
- Use RAG retrieval instead of dumping documents

### Layer 2: RTK (Command Output Compression)
- Wraps git, cargo, npm, docker, kubectl, etc.
- Smart filtering, grouping, truncation, deduplication
- Transparent hook system (auto-rewrite)

**Token Savings:**
| Command | Standard | RTK | Savings |
|---|---|---|---|
| ls / tree | 2,000 | 400 | -80% |
| cat / read | 40,000 | 12,000 | -70% |
| grep / rg | 16,000 | 3,200 | -80% |
| git status | 3,000 | 600 | -80% |
| git diff | 10,000 | 2,500 | -75% |
| cargo test | 25,000 | 2,500 | -90% |
| **Total** | **~118,000** | **~23,900** | **-80%** |

### Layer 3: Caveman (Response Compression)
- Reduces repeated status reports
- Compresses agent-to-agent chatter
- -75% on response output tokens

### Layer 4: code-review-graph (Code Context)
- Builds knowledge graph of codebase using Tree-sitter AST
- Maps functions, classes, imports, call chains
- Computes "blast radius" for changes
- Gives AI only the files that matter

**Token Savings:**
| Repo | Naive Tokens | Graph Tokens | Reduction |
|---|---|---|---|
| fastapi | 4,944 | 614 | 8.1x |
| flask | 44,751 | 4,252 | 9.1x |
| gin | 21,972 | 1,153 | 16.4x |
| nextjs | 9,882 | 1,249 | 8.0x |
| **Average** | | | **8.2x** |

### Layer 5: LSP / AST-grep
- Deterministic symbol lookup and structural edits
- IDE-level precision for code navigation

### Layer 6: RAG Retrieval
- Retrieve only relevant knowledge chunks
- Do not dump entire docs
- Source-of-truth hierarchy enforcement

## Token Pipeline

```text
┌─────────────────────────────────────────────────────────────┐
│                    TOKEN PIPELINE                             │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐  │
│  │   INPUT      │    │   CONTEXT    │    │   OUTPUT     │  │
│  │   Tokens     │    │   Tokens     │    │   Tokens     │  │
│  └──────┬───────┘    └──────┬───────┘    └──────┬───────┘  │
│         │                   │                   │           │
│         ▼                   ▼                   ▼           │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐  │
│  │  code-review │    │  (neither)   │    │    RTK       │  │
│  │  graph       │    │              │    │  (commands)  │  │
│  │  (code read) │    │              │    │              │  │
│  └──────────────┘    └──────────────┘    └──────────────┘  │
│                                                              │
│  What AI reads       What AI keeps      What AI outputs     │
│  from codebase       in context         to user             │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

## Combined Savings

### Current Stack (RTK + Caveman)
- RTK (commands): -60-90% on shell output
- Caveman (responses): -75% on AI output
- **Total: ~70-80% on output tokens**

### With code-review-graph Added
- RTK (commands): -60-90% on shell output
- Caveman (responses): -75% on AI output
- CRG (code context): -80-90% on code reads
- **Total: ~85-90% on all tokens**

## Implementation Recommendations

### Immediate
- Keep RTK — daily driver, always useful
- Keep Caveman — response optimization

### When Codebase Grows (500+ files)
- Install code-review-graph
- Set up MCP integration
- Use blast radius for reviews

### Hybrid Stack (Optimal)
```text
Token Optimization Stack:
├─ Layer 1: RTK (command output) → -80%
├─ Layer 2: Caveman (response) → -75%
├─ Layer 3: code-review-graph (code context) → -8.2x
└─ Total: ~90% token reduction
```

## Token Impact Analysis

| Approach | Token Usage | Quality | Speed | Complexity |
|---|---|---|---|---|
| SOP.md alone | Medium | High | Slow | Low |
| BMAD alone | High | Very High | Medium | Medium |
| OMO alone | Very High | High | Very Fast | High |
| **Hybrid** | **Medium** | **Very High** | **Fast** | **Medium** |

## Related Pages

- [[ai-development-workflow-analysis]] - Framework comparison and integration
- [[multi-agent-workflow-framework]] - Framework specification
- [[hermes-internals]] - Runtime details and optimization tips
