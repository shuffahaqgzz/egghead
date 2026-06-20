# Hermes Multi-Agent Architecture — Architecture

**Version:** 1.0.0  
**Date:** 2026-05-07  
**Related To:** [00-summary.md](./00-summary.md), [01-implementation.md](./01-implementation.md)

---

## Overview

High-level architecture document untuk Hermes multi-agent setup. Menjelaskan design decisions, resource allocation, security model, dan scalability considerations.

---

## System Architecture

### Infrastructure

```
┌─────────────────────────────────────────────────────────────────┐
│                        srv-test01                               │
│                        (Linux VM)                                │
│                        User: infra                               │
│                        RAM: Total System                         │
│                                                                 │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │                    Hermes Agent                          │  │
│  │                    (Python App)                           │  │
│  │                   ~/.hermes/                              │  │
│  │                                                           │  │
│  │  ┌─────────────────────────────────────────────────────┐  │  │
│  │  │              hermes-agent/                          │  │  │
│  │  │              (Source Code)                          │  │  │
│  │  │              venv/lib/python3.11/                   │  │  │
│  │  └─────────────────────────────────────────────────────┘  │  │
│  │                                                           │  │
│  │  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐     │  │
│  │  │  Profile    │  │  Profile    │  │  Profile    │     │  │
│  │  │  default    │  │  coda       │  │  resa       │     │  │
│  │  │             │  │             │  │             │     │  │
│  │  │  auth.json  │  │  auth.json  │  │  auth.json  │     │  │
│  │  │  config    │  │  config     │  │  config     │     │  │
│  │  │  .env      │  │  .env       │  │  .env       │     │  │
│  │  │  memory/   │  │  memory/    │  │  memory/    │     │  │
│  │  │  sessions/ │  │  sessions/  │  │  sessions/  │     │  │
│  │  │  skills/   │  │  skills/    │  │  skills/    │     │  │
│  │  └─────────────┘  └─────────────┘  └─────────────┘     │  │
│  │                          ┌─────────────┐               │  │
│  │                          │  Profile    │               │  │
│  │                          │  sevi       │               │  │
│  │                          │             │               │  │
│  │                          │  auth.json  │               │  │
│  │                          │  config     │               │  │
│  │                          │  .env       │               │  │
│  │                          │  memory/    │               │  │
│  │                          │  sessions/  │               │  │
│  │                          │  skills/    │               │  │
│  │                          └─────────────┘               │  │
│  └───────────────────────────────────────────────────────────┘  │
│                                                                 │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │              Systemd Services                             │  │
│  │  ┌─────────────────────────────────────────────────────┐  │  │
│  │  │  hermes-gateway-default.service  (PID: 2921994)   │  │  │
│  │  │  hermes-gateway-coda.service     (PID: 808354)    │  │  │
│  │  │  hermes-gateway-resa.service    (PID: 1152751)   │  │  │
│  │  │  hermes-gateway-sevi.service    (PID: 1210035)   │  │  │
│  │  └─────────────────────────────────────────────────────┘  │  │
│  └───────────────────────────────────────────────────────────┘  │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
         │                    │                    │
         │ Telegram           │ Discord            │ Discord
         │ (Default only)     │ (All profiles)    │
         ▼                    ▼                    ▼
    ┌──────────┐        ┌──────────┐          ┌──────────┐
    │ Telegram │        │ Discord  │          │ Discord  │
    │  Bot     │        │  Bot 1   │          │  Bot 2   │
    │ (Kumachii)│       │ (CODA)  │          │ (RESA)  │
    └──────────┘        └──────────┘          └──────────┘
                                                 │
                                                 ▼
                                            ┌──────────┐
                                            │ Discord  │
                                            │  Bot 3   │
                                            │ (SEVI)  │
                                            └──────────┘
```

---

## Agent Distribution

### Default Profile — Kumachii 🤘

**Role:** Main assistant, hub for all operations

**Platforms:**
- Telegram: Home DM
- Discord: Home channel

**Resources:**
- RAM: ~331MB
- PID: 2921994

**Sub-Agents (Light):**
| Agent | Channel | Platform | Role |
|-------|---------|----------|------|
| WOOF | 1501446154955657256 | Discord | Office Operations |
| CONCRET | 1501446213130649640 | Discord | Content Operations |
| LICA | 1501446282894512228 | Discord | Education/Life Ops |
| FINA | 1501446306512502845 | Discord | Finance Operations |
| DOCA | 1501446326523527239 | Discord | Document Operations |

---

## Profile Agents

### CODA — Coding Agent 🤖

**Profile:** `coda`  
**Purpose:** Specialized coding assistant

**Platforms:**
- Discord only
- Channel: 1501447521086476348

**Resources:**
- RAM: ~94MB
- PID: 808354

**Model:**
- Provider: EnowXAI (custom)
- Model: claude-opus-4.6
- Base URL: http://10.50.0.111:1430/v1

---

### RESA — Research Agent 🔬

**Profile:** `resa`  
**Purpose:** Research & analysis

**Platforms:**
- Discord only
- Channel: 1501447723000266793

**Resources:**
- RAM: ~94MB
- PID: 1152751

**Model:**
- Provider: opencode-go
- Model: glm-5.1
- Base URL: (default from credential)

---

### SEVI — Security Agent 🔒

**Profile:** `sevi`  
**Purpose:** Security analysis & threat assessment

**Platforms:**
- Discord only
- Channel: 1501447906396344320

**Resources:**
- RAM: ~94MB
- PID: 1210035

**Model:**
- Provider: openai-codex
- Model: gpt-5.5
- Base URL: (default from credential)

---

## Resource Allocation

### Memory Usage

| Profile | Gateway | RAM (approx) | % of Total |
|---------|---------|--------------|------------|
| default | hermes-gateway-default | ~331MB | 53% |
| coda | hermes-gateway-coda | ~94MB | 15% |
| resa | hermes-gateway-resa | ~94MB | 15% |
| sevi | hermes-gateway-sevi | ~94MB | 15% |
| **Total** | | **~621MB** | **100%** |

### Cost Optimization

**Light Agents (WOOF, CONCRET, LICA, FINA, DOCA):**
- Zero additional RAM — run inside default gateway process
- Zero additional compute overhead beyond inference
- Only additional cost: system prompt tokens per message

**Dedicated Agents (CODA, RESA, SEVI):**
- Each ~94MB dedicated process
- Isolation benefits outweigh cost
- Per-agent model specialization = better quality per $

---

## Security Model

### Profile Isolation

Each profile is **fully isolated**:

```
┌─────────────────────────────────────────────────────────────┐
│                    ~/.hermes/                              │
│                                                             │
│  default/           coda/           resa/           sevi/  │
│  ├── auth.json     ├── auth.json    ├── auth.json   ├── auth.json
│  ├── config.yaml   ├── config.yaml  ├── config.yaml  ├── config.yaml
│  ├── .env          ├── .env         ├── .env         ├── .env
│  ├── memory/       ├── memory/      ├── memory/      ├── memory/
│  ├── sessions/     ├── sessions/    ├── sessions/    ├── sessions/
│  └── skills/       └── skills/      └── skills/       └── skills/
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

**Implications:**
- Credentials in one profile CANNOT access another profile's resources
- Session history is per-profile
- Memory is per-profile
- Skills are per-profile (unless symlinked)

### Credential Storage

```
~/.hermes/auth.json (global)
    └── credential_pool:
        ├── "custom:enowxai": [...]
        ├── "opencode-go": [...]
        └── "openai-codex": [...]

~/.hermes/profiles/coda/auth.json
    └── credential_pool:
        └── "custom:enowxai": [...]  ← COPY from global

~/.hermes/profiles/resa/auth.json
    └── credential_pool:
        └── "opencode-go": [...]  ← COPY from global

~/.hermes/profiles/sevi/auth.json
    └── credential_pool:
        └── "openai-codex": [...]  ← COPY from global
```

### Platform Access Control

**Telegram:**
- Default profile only
- Other profiles: DISABLED (not just lacks token — explicitly disabled)

**Discord:**
- One bot per dedicated profile
- Each bot has its own token
- Each bot operates in its own channel only

---

## Communication Patterns

### User → Agent

```
User (Telegram/Discord)
    │
    ▼
┌─────────────────────────────────────────┐
│  Platform Gateway (Telegram/Discord)    │
│  - Validates token                      │
│  - Routes to correct profile gateway    │
└─────────────────────────────────────────┘
    │
    ▼
┌─────────────────────────────────────────┐
│  Profile Gateway (hermes-gateway-<x>)   │
│  - Loads profile config                 │
│  - Initializes agent                    │
│  - Routes to correct agent/channel      │
└─────────────────────────────────────────┘
    │
    ▼
┌─────────────────────────────────────────┐
│  Agent (LLM + Tools)                    │
│  - Receives message                     │
│  - Processes with context               │
│  - Returns response                     │
└─────────────────────────────────────────┘
```

### Agent → Agent (Future)

Currently agents are isolated. Future patterns:
- Internal message passing via shared database
- Agent delegation via `delegate_task` skill
- Shared knowledge base (Obsidian vault)

---

## Scalability Considerations

### Current Limits

| Resource | Current | Limit | Usage |
|----------|---------|-------|-------|
| Profiles | 4 | Unknown | ~10-20 typical |
| Gateways | 4 | Unknown | ~5-10 typical |
| RAM (gateways) | ~621MB | System dependent | Low |
| Concurrent messages | Per-gateway | Unknown | Low |

### Scaling Options

**Vertical:**
- Increase gateway RAM if needed
- Add more toolsets per agent

**Horizontal:**
- Add more profiles for more agents
- Shard across different machines
- Load balancing across multiple instances

---

## Failure Modes

### Gateway Crash

**Symptom:** `hermes profile list` shows gateway as "stopped"

**Recovery:**
```bash
systemctl --user start hermes-gateway-<name>.service
```

**Auto-restart:** Services have `Restart=always` — should auto-recover

---

### Token Conflict

**Symptom:** Gateway won't start, "bot token already in use"

**Cause:** Another gateway already holding the same token

**Fix:** 
1. Stop the conflicting gateway
2. Or ensure each profile uses unique tokens

---

### Model Provider Auth Failure

**Symptom:** `401 AuthenticationError` in logs

**Cause:** 
- Stale API key
- Wrong base_url
- Missing credential in profile's auth.json

**Fix:**
```bash
<name> config set model.base_url ""
<name> config set model.api_key ""
# Ensure credential exists in ~/.hermes/profiles/<name>/auth.json
```

---

## Future Architecture Enhancements

1. **Agent-to-Agent Communication**
   - Shared message queue
   - Delegation via skills

2. **Shared Knowledge Base**
   - Centralized Obsidian vault
   - Per-profile knowledge graphs

3. **Load Balancing**
   - Multiple instances of same agent
   - Traffic routing based on load

4. **Centralized Monitoring**
   - Prometheus metrics
   - Grafana dashboards
   - Alerting on gateway failures
