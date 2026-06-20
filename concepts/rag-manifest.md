---
title: RAG Manifest
created: 2026-06-20
updated: 2026-06-20
type: concept
tags: [memory, shared-state, persistence]
sources: [_archive/MANIFEST.md]
confidence: high
---

# RAG Manifest

The RAG manifest tracks all documents ingested into the knowledge base for a project. It ensures [[authority-hierarchy|source authority]] is respected, staleness is detected, and no secrets leak into the retrieval system.

## Manifest Structure

```yaml
topic: <project-name>
task_id: <task-id>
project: <project-name>
last_ingested: <ISO-timestamp>
ingested_by: <agent>       # Usually BONNEY
status: active | archived
```

## Ingested Sources Table

Each source is tracked with:

| Field | Purpose |
|-------|---------|
| File | Original document name |
| Source Type | prd, architecture, adr, review, security, gate, runbook |
| Owner | Agent responsible for the document |
| Authority | Rank in [[authority-hierarchy\|authority hierarchy]] (lower = higher) |
| Freshness | fresh / stale / expired |
| Status | active / archived |

## Ingestion Rules (BONNEY)

From [[coding-rules|AGENTS.md]] RAG rules:
- Source-of-truth documents outrank summaries
- Never ingest secrets
- Mark stale knowledge
- Include metadata
- Validate retrieval quality

## Example: hermes-pingback Manifest

| File | Type | Owner | Authority | Freshness |
|------|------|-------|-----------|-----------|
| ARCHITECTURE.md | architecture | SHAKA | 2 | fresh |
| ADR-001-FRAMEWORK.md | adr | SHAKA | 2 | fresh |
| SECURITY_REVIEW.md | security | ATLAS | 2 | fresh |
| PRD.md | prd | STELLA | 3 | fresh |
| GATES.md | gate | STELLA | 3 | fresh |
| CODE_REVIEW.md | review | YORK | 4 | fresh |
| RUNBOOK.md | runbook | BONNEY | 5 | fresh |

Authority hierarchy: architecture/security (2) > PRD/gates (3) > code review (4) > runbook (5).

## Notes

- Gate docs consolidated into single GATES.md for retrieval efficiency
- No secrets found in any source
- All docs ingested with metadata headers per RAG-INGESTION skill schema

## Related Concepts

- [[authority-hierarchy]] — How authority ranks are assigned
- [[coding-rules]] — RAG ingestion rules
- [[overall-architecture]] — Documents tracked in manifests
- [[gate-system]] — Gate documents in manifest
