---
source_file: /notflix/apps/hermes-pingback/docs/runbook.md
source_type: runbook
owner_agent: BONNEY
created_at: 2026-05-16T19:47:00+07:00
updated_at: 2026-05-16T19:47:00+07:00
project: hermes-pingback
task_id: E2E-LIFECYCLE-001
phase: 6
version: 0.1.0
freshness: fresh
authority_level: 5
contains_secret: false
---

# Runbook: hermes-pingback

## Service Overview

| Property | Value |
|----------|-------|
| Host IP | 10.50.0.111 |
| Port | 8111 |
| Network | notflix_network (Docker bridge, external) |
| Traefik | Disabled |
| Base image | python:3.12-slim |
| Resources | 0.25 CPU, 64MB RAM |
| Filesystem | Read-only |
| Auth | None (LAN trust model) |

## Operations

### Start

```bash
cd /notflix/apps/hermes-pingback
docker compose --profile pingback up -d
```

### Stop

```bash
docker compose --profile pingback down
```

### Health Check

```bash
curl -s http://10.50.0.111:8111/health | jq .
```

Expected: `{"status":"ok","version":"0.1.0","timestamp":"...","service":"hermes-pingback","uptime_seconds":...}`

### Rebuild

```bash
docker compose --profile pingback up -d --build
```

### Rollback

```bash
docker compose --profile pingback down
docker rmi hermes-pingback:latest
# Revert code if needed: git checkout HEAD~1 -- src/ Dockerfile
docker compose --profile pingback up -d --build
```

## Troubleshooting

- Port conflict → `ss -tlnp | grep 8111`, kill conflicting process
- Crash loop → `docker logs hermes-pingback --tail 50`
- Network missing → `docker network create notflix_network`
- Unhealthy → `docker inspect hermes-pingback --format='{{json .State.Health}}'`

## Escalation

1. Automated alert → STUSSY investigates
2. Unresolved → STELLA
3. Infrastructure → Gilang

## Monitoring

- Docker healthcheck: every 30s, 3 retries
- uptime-kuma: polls /health endpoint
- Health watchdog cronjob (STUSSY)
