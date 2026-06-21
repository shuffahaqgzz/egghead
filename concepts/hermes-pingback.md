---
title: "Hermes Pingback"
created: 2026-06-20
updated: 2026-06-20
type: concept
tags: [project, hermes-pingback, health-check, deployment]
sources: [_archive/project-specific/ARCHITECTURE.md, _archive/project-specific/PRD.md]
confidence: medium
---

# Hermes Pingback

Simple health-check service untuk memverifikasi Hermes Agent runtime availability.

## Overview

Single-purpose Python service yang menjalankan health check endpoint. Digunakan sebagai E2E test case untuk framework lifecycle workflow.

## Architecture

- Single-purpose Python service (stdlib http.server)
- Docker Compose deployment on shared LAN network
- LAN-only binding, no public exposure
- Non-root, read-only filesystem, minimal privileges

## ADR

See [[framework-selection-adr]] for HTTP framework selection decision (stdlib over FastAPI/Flask).

## Project-Specific Docs

Source docs di `_archive/project-specific/`:
- PRD.md, ARCHITECTURE.md, ADR-001-FRAMEWORK.md
- CODE_REVIEW.md, SECURITY_REVIEW.md, GATES.md
- RUNBOOK.md, MANIFEST.md

## See Also

- Deployment architecture (see `_archive/framework-level/deployment-pattern.md`)
- Security considerations (see `_archive/framework-level/security-model.md`)
