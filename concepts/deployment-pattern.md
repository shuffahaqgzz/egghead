---
title: Deployment Pattern
created: 2026-06-20
updated: 2026-06-20
type: concept
tags: [deployment, docker, scaling, architecture]
sources: [_archive/RUNBOOK.md, _archive/ARCHITECTURE.md, _archive/AGENT_DEPLOYMENT_TEMPLATE.md]
confidence: high
---

# Deployment Pattern

The framework uses two distinct deployment patterns: **service deployment** (Docker Compose for application services) and **agent deployment** (systemd + Hermes profiles for AI agents). Both follow principle of least privilege and structured provisioning.

## Service Deployment (Docker Compose)

Application services like [[hermes-pingback]] deploy via Docker Compose on the `notflix_network` bridge.

### Standard Service Template

| Property | Pattern |
|----------|---------|
| Base image | `python:3.12-slim` (minimal) |
| Network | `notflix_network` (external Docker bridge) |
| Port binding | LAN IP only (e.g., `10.50.0.111:8111`) |
| Traefik | Disabled (`traefik.enable=false`) |
| Restart | `unless-stopped` |
| Filesystem | Read-only |
| Resources | Capped (CPU + RAM) |
| Healthcheck | Built-in (no curl in slim — use python urllib) |

### Operations

```bash
# Start
docker compose --profile <service> up -d

# Stop
docker compose --profile <service> down

# Rebuild
docker compose --profile <service> up -d --build

# Rollback
docker compose --profile <service> down
docker rmi <service>:latest
git checkout HEAD~1 -- src/ Dockerfile
docker compose --profile <service> up -d --build
```

## Agent Deployment (Hermes Profiles)

Each AI agent runs as a dedicated Hermes Agent profile with its own systemd service, Discord bot, and environment.

### Provisioning Steps

1. **Create profile:** `hermes profile create <agent_name>`
2. **Copy config:** Clone STELLA's config, patch Discord channels
3. **Write .env:** Bot token, Discord channels, API keys
4. **Write SOUL.md:** Agent personality and role definition
5. **Create systemd unit:** `~/.config/systemd/user/hermes-gateway-<agent_name>.service`
6. **Enable + start:** `systemctl --user enable --now hermes-gateway-<agent_name>.service`
7. **Verify:** Chat test to confirm name and role

### Systemd Unit Pattern

```ini
[Service]
Type=simple
ExecStart=/home/infra/.hermes/hermes-agent/venv/bin/python -m hermes_cli.main \
  --profile <agent_name> gateway run --replace
Restart=on-failure
RestartSec=5
```

### Agent Isolation

Each profile maintains:
- Separate Discord bot token and channel
- Separate LLM model and provider
- Separate credentials, config, session, and memory
- No token conflict between concurrent gateways

## Escalation Chain

1. Automated alert → STUSSY investigates
2. Unresolved → STELLA
3. Infrastructure → Human (Gilang)

## Monitoring

- Docker healthcheck: every 30s, 3 retries
- uptime-kuma: polls `/health` endpoint
- Health watchdog cronjob (STUSSY)

## Related Concepts

- [[security-model]] — Container hardening requirements
- [[overall-architecture]] — Infrastructure layer
- [[agent-roster]] — Agents deployed via this pattern
- [[gate-system]] — Deployment gate (Phase 5)
