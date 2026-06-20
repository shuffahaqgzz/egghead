---
title: Authority Hierarchy
created: 2026-06-20
updated: 2026-06-20
type: concept
tags: [meta, best-practice]
sources: [_archive/AGENTS.md, _archive/MANIFEST.md]
confidence: high
---

# Authority Hierarchy

The authority hierarchy defines which sources of information take precedence when conflicts arise. This prevents generated summaries from overriding primary sources and ensures architectural decisions bind implementation.

## Hierarchy (Highest to Lowest)

| Rank | Source Type | Example |
|------|------------|---------|
| 1 | Source-of-truth documents | Official specs, blessed designs |
| 2 | Architecture + ADRs | [[overall-architecture\|Architecture docs]], [[framework-selection-adr\|ADRs]] |
| 3 | PRD + acceptance criteria | Product requirements, test cases |
| 4 | Code + tests | Implementation artifacts |
| 5 | Deployment + runbook docs | [[deployment-pattern\|Runbooks]], deployment configs |
| 6 | Research reports | PYTHAGORAS outputs |
| 7 | Generated summaries | BONNEY RAG summaries |
| 8 | Chat history | Discord/Telegram conversations |

## Key Rule

> **Generated summaries must not override primary sources.**

This means:
- If a RAG summary contradicts the architecture doc, the architecture doc wins
- If code contradicts the PRD, the PRD wins (and code must be fixed)
- If a research report contradicts an ADR, the ADR wins (but the ADR may be revisited)

## Application in Gate Evaluation

During [[gate-system|gate reviews]], evaluators check:
1. Does the implementation comply with architecture (rank 2)?
2. Does the implementation satisfy PRD acceptance criteria (rank 3)?
3. Are deployment artifacts consistent with the runbook (rank 5)?

## RAG Manifest Authority

From [[rag-manifest]], the RAG manifest tracks source authority levels:
- Architecture / Security reviews: authority level 2
- PRD / Gates: authority level 3
- Code reviews: authority level 4
- Runbooks: authority level 5

Lower authority_level number = higher precedence.

## Related Concepts

- [[gate-system]] — Where authority hierarchy is enforced
- [[overall-architecture]] — Architecture as rank-2 authority
- [[rag-manifest]] — How RAG tracks source authority
- [[coding-rules]] — ADR compliance in implementation
