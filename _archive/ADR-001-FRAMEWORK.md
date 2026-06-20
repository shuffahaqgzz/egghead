---
source_file: /notflix/apps/hermes-pingback/docs/adr/001-http-framework-selection.md
source_type: adr
owner_agent: SHAKA
created_at: 2026-05-16T11:30:00+07:00
updated_at: 2026-05-16T11:30:00+07:00
project: hermes-pingback
task_id: E2E-LIFECYCLE-001
phase: 2
version: 0.1.0
freshness: fresh
authority_level: 2
contains_secret: false
---

# ADR-001: HTTP Framework Selection

**Status:** Accepted
**Date:** 2026-05-16

## Decision

Python stdlib `http.server` over FastAPI or Flask.

## Reasoning

1. Scope trivially small — one endpoint, one JSON response, no validation needed
2. Zero dependencies = zero supply chain risk
3. Minimal image size and fastest cold start (~100ms vs ~500ms FastAPI)
4. Testability manageable for single endpoint (integration test sufficient)
5. Consistency with purpose — simplest thing that works for "are you alive?"

## Options Considered

- **Option A: stdlib http.server** — CHOSEN. Zero deps, 50MB image, 100ms cold start
- **Option B: FastAPI** — Overkill. 2 deps, 80MB image, 300-500ms cold start
- **Option C: Flask** — Middle ground but still unnecessary complexity

## Consequences

- Positive: Smallest attack surface, fastest cold start, zero dependency maintenance
- Negative: If scope grows beyond 2-3 endpoints, should migrate to FastAPI

## Migration Trigger

Revisit if: >3 endpoints, request body parsing, auth needed, or structured middleware needed.
