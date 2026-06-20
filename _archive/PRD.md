---
source_file: /notflix/apps/hermes-pingback/docs/prd-E2E-LIFECYCLE-001.md
source_type: prd
owner_agent: STELLA
created_at: 2026-05-16T11:00:00+07:00
updated_at: 2026-05-16T11:00:00+07:00
project: hermes-pingback
task_id: E2E-LIFECYCLE-001
phase: 1
version: 0.1.0
freshness: fresh
authority_level: 3
contains_secret: false
---

# PRD: hermes-pingback

**Task ID:** E2E-LIFECYCLE-001
**Version:** 0.1.0
**Owner:** STELLA

## Objective

Deploy minimal LAN health-check service `hermes-pingback` sebagai E2E lifecycle test untuk validate framework v1.1.0 full Phase 0→6.

## Functional Requirements

- FR-1: `GET /health` returns HTTP 200 with body `{"status":"ok","version":"0.1.0"}`
- FR-2: Response content-type: `application/json`

## Non-Functional Requirements

- NFR-1: Service bind ke `10.50.0.111:8111`
- NFR-2: Join `notflix_network` (external)
- NFR-3: Traefik disabled (`traefik.enable=false`)
- NFR-4: Container auto-restart (`restart: unless-stopped`)
- NFR-5: LAN-only, no public exposure

## Acceptance Criteria

- AC-1: `curl -s -o /dev/null -w "%{http_code}" http://10.50.0.111:8111/health` → 200
- AC-2: `curl -s http://10.50.0.111:8111/health | jq .` → `{"status":"ok","version":"0.1.0"}`
- AC-3: Content-type header is `application/json`

## Out-of-Scope

- Authentication / authorization
- Database / persistence
- Multiple endpoints
- Public exposure / reverse proxy
- Logging/monitoring stack (Phase 6)
- CI/CD pipeline

## Agent Participation

| Agent | Phase | Role |
|-------|-------|------|
| KUMACHII | 0 | Intake |
| STELLA | 0-6 | Orchestrator |
| SHAKA | 2 | Architecture |
| ATLAS | 2, 4 | Security |
| EDISON | 3 | Implementation |
| YORK | 4 | QA |
| LILITH | 5 | Deployment |
| STUSSY | 6 | Monitoring |
| BONNEY | 6 | Runbook/docs |
