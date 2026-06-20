---
title: Security Model
created: 2026-06-20
updated: 2026-06-20
type: concept
tags: [security, sandbox, permission, isolation, trust, auth]
sources: [_archive/SECURITY_REVIEW.md, _archive/ARCHITECTURE.md, _archive/AGENTS.md]
confidence: high
---

# Security Model

The security model combines **container hardening**, **LAN trust boundaries**, and **role-based blocking authority** to protect the multi-agent system. Security is enforced at three layers: infrastructure (Docker), network (LAN-only), and agent (ATLAS review gate).

## Container Hardening

Every deployed service must satisfy these requirements (verified by ATLAS):

| Requirement | Implementation |
|-------------|----------------|
| Non-root user | `appuser` with `/bin/false` shell |
| Capabilities dropped | `cap_drop: ALL` |
| No privilege escalation | `no-new-privileges: true` |
| Read-only filesystem | `read_only: true` in Docker Compose |
| Resource limits | CPU and RAM caps (e.g., 0.25 CPU, 64MB RAM) |
| Minimal base image | `python:3.12-slim` (no unnecessary packages) |
| LAN-only binding | Bind to specific IP (e.g., `10.50.0.111`), not `0.0.0.0` |
| Reverse proxy disabled | `traefik.enable=false` |

## Network Trust Model

- **LAN-only:** Services bind to internal IPs; no public exposure
- **No authentication:** Internal network trust — services assume callers are authorized
- **Docker bridge network:** `notflix_network` isolates container traffic

This trust model is appropriate for single-purpose internal health checks and monitoring services. Services requiring external access must implement authentication.

## Security Review Process (ATLAS)

ATLAS performs post-implementation review at Gate 4, checking:

| Dimension | What's Verified |
|-----------|----------------|
| Secrets management | No secrets in code, config, or environment |
| Input validation | Exact path matching, no traversal attacks |
| Dependencies | Zero or minimal external dependencies |
| Logging & privacy | No PII leaks |
| Configuration | Container hardened per checklist |
| Graceful shutdown | Signal handlers registered, clean exit |

Severity ratings: CRITICAL / HIGH / MEDIUM / LOW / INFO. CRITICAL and HIGH findings block deployment.

## Agent Security Rules

From [[coding-rules|AGENTS.md]], ATLAS must:
- Never expose secrets
- Use least privilege
- Validate inputs
- Check dependency risk
- Block critical issues
- **Never approve security exceptions alone** — requires STELLA approval

## Graceful Shutdown Pattern

Services implement signal-based shutdown:
```python
SIGTERM/SIGINT → Event set → server.shutdown() + server_close() → thread join(timeout=5)
```
Verified by ATLAS for race conditions and clean exit (returncode 0).

## Related Concepts

- [[overall-architecture]] — How security fits into the system
- [[gate-system]] — Security gate at Phase 4
- [[coding-rules]] — Security rules for implementation
- [[deployment-pattern]] — Hardened deployment template
