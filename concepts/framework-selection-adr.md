---
title: "Framework Selection ADR"
created: 2026-06-20
updated: 2026-06-20
type: concept
tags: [architecture, adr, framework, decision]
sources: []
confidence: high
---

# Framework Selection ADR

Architecture Decision Record untuk pemilihan framework multi-agent.

## Decision

Gunakan Egghead AI-Driven Multi-Agent Framework sebagai baseline framework untuk semua project Gilang.

## Reasoning

1. Hermes Agent runtime sudah mature dan stabil
2. MattPocock + Superpowers + BMAD + OMO patterns sudah terbukti
3. Generic role names memungkinkan portabilitas
4. Phase-gated workflow memberikan structure tanpa over-engineering
5. Skill-first approach memungkinkan extensibility

## Options Considered

- **Option A: Egghead MAF** — Custom framework combining 4 methodologies. CHOSEN.
- **Option B: BMAD direct install** — Full BMAD method. REJECTED (too heavy for solo dev, designed for Claude Code/Cursor).
- **Option C: Superpowers only** — Skills-only approach. REJECTED (lacks workflow structure).
- **Option D: OMO only** — Multi-model orchestration. REJECTED (tied to OpenCode runtime).

## Consequences

- Positive: Portable, extensible, Hermes-native, proven patterns
- Negative: Custom maintenance, no upstream community

## See Also

- [[egghead-framework]] — Framework overview
- [[workflow-lifecycle]] — Phase details
