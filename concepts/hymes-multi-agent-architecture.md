---
title: Hermes Multi-Agent Architecture
created: 2026-06-20
updated: 2026-06-20
type: concept
tags:
  - hermes
  - multi-agent
  - architecture
  - implementation
  - deployment
sources:
  - 00-summary.md
  - 01-implementation.md
  - 02-configuration.md
  - 03-architecture.md
  - 04-commissioning-testing.md
---

# Hermes Multi-Agent Architecture

The Hermes Multi-Agent Architecture is a hybrid multi-agent system deployed on the Hermes Agent framework (Nous Research). It uses 8 specialized agents with two deployment phases: Light Agents (channel prompts, zero additional RAM) and Dedicated Profile Agents (isolated profiles with dedicated bot tokens and models).

## Architecture Overview

```text
┌─────────────────────────────────────────────────────────────────┐
│                      srv-test01 (Linux)                         │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                   default profile                        │   │
│  │                   (Kumachii)                             │   │
│  │  Model: claude-sonnet-4.5 @ EnowXAI                     │   │
│  │  Gateway: Telegram + Discord                             │   │
│  │                                                          │   │
│  │  LIGHT AGENTS (channel_prompts) — Zero RAM overhead     │   │
│  │  WOOF → OfficeOps    CONCRET → ContentOps              │   │
│  │  LICA → EduLifeOps   FINA → FinanceOps                 │   │
│  │  DOCA → DocOps                                         │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                 │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐             │
│  │   coda      │  │   resa      │  │   sevi      │             │
│  │   (CODA)    │  │   (RESA)    │  │   (SEVI)    │             │
│  │ claude-opus │  │ glm-5.1     │  │ gpt-5.5     │             │
│  │ Discord only│  │ Discord only│  │ Discord only│             │
│  └─────────────┘  └─────────────┘  └─────────────┘             │
└─────────────────────────────────────────────────────────────────┘
```

## Agent Types

### Light Agents (Channel Prompts)
Run inside the default profile gateway via `channel_prompts` configuration. Zero additional RAM overhead.

| Agent | Channel ID | Role |
|-------|------------|------|
| WOOF | 1501446154955657256 | Office Operations |
| CONCRET | 1501446213130649640 | Content Operations |
| LICA | 1501446282894512228 | Education/Life Operations |
| FINA | 1501446306512502845 | Finance Operations |
| DOCA | 1501446326523527239 | Document Operations |

### Dedicated Profile Agents
Each runs as an isolated Hermes profile with dedicated bot token, channel, and model.

| Profile | Agent | Model | Provider | Gateway |
|---------|-------|-------|----------|---------|
| default | Kumachii | claude-sonnet-4.5 | EnowXAI | PID 2921994 |
| coda | CODA (Coding) | claude-opus-4.6 | EnowXAI | PID 808354 |
| resa | RESA (Research) | glm-5.1 | opencode-go | PID 1152751 |
| sevi | SEVI (Security) | gpt-5.5 | openai-codex | PID 1210035 |

## Key Discoveries

### Token Conflict Resolution
Bot tokens cannot be shared between profiles. Solution: disable Telegram in dedicated profiles and use dedicated Discord bots per profile.

### Credential Isolation
Profiles use separate `auth.json` files. Credentials are not auto-shared. Must copy manually from `~/.hermes/auth.json` to `~/.hermes/profiles/<name>/auth.json`.

### Model Configuration
Provider switching requires clearing `base_url` and `api_key` to use defaults from the credential pool.

### Resource Usage
Total RAM: ~621MB (4 gateways). Light agents: zero additional overhead.

## Configuration Reference

### File Structure
```text
~/.hermes/
├── auth.json                    # Global credentials (default profile)
├── config.yaml                  # Global/default config
├── .env                         # Global environment variables
├── profiles/
│   ├── coda/
│   │   ├── auth.json           # CODA credentials
│   │   ├── config.yaml         # CODA config
│   │   ├── .env               # CODA env vars
│   │   ├── memory/            # CODA session memory
│   │   ├── sessions/          # CODA conversation history
│   │   └── skills/            # CODA custom skills
│   ├── resa/
│   └── sevi/
```

### Systemd Services
```text
~/.config/systemd/user/
├── hermes-gateway-default.service
├── hermes-gateway-coda.service
├── hermes-gateway-resa.service
└── hermes-gateway-sevi.service
```

## Commissioning & Testing

### Pre-Commissioning Checklist
- Infrastructure accessible (srv-test01, user: infra)
- Hermes Agent installed (`~/.hermes/hermes-agent/`)
- All profiles created and configured
- Platform tokens validated (Telegram: default only, Discord: one per profile)

### Test Categories
1. **Gateway Startup Tests** — Verify each gateway starts and auto-restarts
2. **Model Configuration Tests** — Verify correct model per profile
3. **Chat Integration Tests** — Verify each agent responds correctly
4. **Platform Access Tests** — Verify Telegram (default only) and Discord (all)
5. **Light Agent Tests** — Verify all 5 channel prompt agents
6. **Stress Tests** — Concurrent access and rapid fire messages
7. **Security Tests** — Profile isolation and credential isolation

### Rollback Procedures
```bash
# Disable a profile agent
systemctl --user stop hermes-gateway-<name>.service
systemctl --user disable hermes-gateway-<name>.service

# Remove a profile
hermes profile delete <name>
rm -rf ~/.hermes/profiles/<name>
```

## Related Pages

- [[multi-agent-workflow-framework]] - The KUMACHII-STELLA framework v1.1.0
- [[hermes-internals]] - Runtime config, setup, and architecture details
- [[kumachii-vs-hermes-tools]] - Tool mapping between Kumachii and Hermes
- [[enowx-provider]] - EnowX Labs provider configuration
