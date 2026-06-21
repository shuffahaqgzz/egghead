---
title: "Hermes Wiki Research"
created: 2026-06-20
updated: 2026-06-20
type: concept
tags: [wiki, research, llm-wiki, knowledge-base]
sources: []
confidence: medium
---

# Hermes Wiki Research

Riset dan implementasi LLM-Wiki untuk Hermes Agent knowledge base.

## Overview

LLM-Wiki mengikuti pola Karpathy — knowledge base sebagai interlinked markdown files. Berbeda dari RAG tradisional (yang rediscover knowledge per query), wiki compile knowledge sekali dan keep current.

## Key Findings

1. Wiki compiles knowledge once, keeps it current
2. Cross-references already there
3. Contradictions already flagged
4. Synthesis reflects everything ingested
5. Human curates sources, agent summarizes and maintains

## Implementation

- Wiki location: `~/egghead-wiki/` (or configurable via `WIKI_PATH`)
- Three layers: Raw sources (immutable), Wiki pages (agent-owned), Schema (rules)
- Lint script for health checks
- Git-based renewal for upstream sync

## See Also

- [[egghead-framework]] — Framework overview
- [[runtime-integration]] — Knowledge architecture
