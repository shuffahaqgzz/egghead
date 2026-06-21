---
title: "RAG Architecture"
created: 2026-06-20
updated: 2026-06-20
type: concept
tags: [rag, architecture, knowledge, retrieval]
sources: []
confidence: medium
---

# RAG Architecture

Retrieval-Augmented Generation architecture untuk Egghead framework.

## Overview

RAG pipeline memungkinkan agent mengakses knowledge base secara real-time untuk informasi yang relevan.

## Components

1. **LLM-Wiki** — Interlinked markdown knowledge base (Karpathy pattern)
2. **Hindsight** — External memory provider untuk persistent cross-profile memory
3. **Handoffs** — Cross-agent handoff documents
4. **Knowledge Base** — Per-project CONTEXT.md, architecture, conventions

## Authority Hierarchy

1. Source-of-truth documents
2. Architecture + ADRs
3. PRD + acceptance criteria
4. Code + tests
5. Deployment + runbook docs
6. Research reports
7. Generated summaries
8. Chat history

Generated summaries must not override primary sources.

## See Also

- RAG manifest (see `_archive/framework-level/rag-manifest.md`)
- [[runtime-integration]] — Knowledge architecture
- Honcho memory (see `_archive/framework-level/honcho-memory-integration.md`)
