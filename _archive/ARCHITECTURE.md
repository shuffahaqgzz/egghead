---
source_file: /notflix/apps/hermes-pingback/docs/architecture.md
source_type: architecture
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

# Architecture: hermes-pingback

Tiny LAN-only health check service. Single endpoint, zero dependencies, Docker Compose deployment on notflix internal network.

## Component

- **hermes-pingback** container (python:3.12-slim)
- GET /health → JSON response
- Bind: 10.50.0.111:8111
- Traefik: disabled

## API Contract

### GET /health → 200 OK

```json
{
  "status": "ok",
  "timestamp": "2026-05-16T11:30:00Z",
  "service": "hermes-pingback",
  "uptime_seconds": 3600
}
```

Unknown paths → 404 `{"error": "not found"}`
Non-GET methods → 405 `{"error": "method not allowed"}`

## Data Flow

Client (curl/uptime-kuma/hermes) → GET /health → hermes-pingback (stdlib http.server) → compute uptime, format JSON → 200 OK + JSON body

No database. No external calls. No state beyond process start time.

## Security Model

- LAN-only: bound to 10.50.0.111
- traefik.enable=false
- No auth (internal network trust model)
- Read-only filesystem (Docker read_only: true)
- cap_drop: ALL, no-new-privileges
- Non-root user (appuser)

## Deployment Model

- Docker Compose in `/notflix/apps/hermes-pingback/`
- Image: python:3.12-slim (no build step beyond COPY)
- Network: notflix_network (external)
- Port: 8111 (configurable via PORT env)
- Resources: 0.25 CPU, 64MB RAM
- Healthcheck: python urllib (no curl in slim image)

## Implementation

- `src/server.py`: http.server.HTTPServer + BaseHTTPRequestHandler (~92 lines)
- Signal handling: SIGTERM/SIGINT → graceful shutdown via threading.Event
- Port: read from PORT env var, default 8111
