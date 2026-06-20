# Hermes Multi-Agent Architecture — Documentation

**Version:** 1.0.0  
**Date:** 2026-05-07  
**Author:** Rosinante / Kumachii 🤘

---

## Table of Contents

| Document | Description |
|----------|-------------|
| [00-summary.md](./00-summary.md) | Executive summary, quick status, key achievements |
| [01-implementation.md](./01-implementation.md) | Step-by-step implementation, troubleshooting log |
| [02-configuration.md](./02-configuration.md) | Complete configuration reference |
| [03-architecture.md](./03-architecture.md) | System architecture, resource allocation, security model |
| [04-commissioning-testing.md](./04-commissioning-testing.md) | Deployment procedures, testing protocols |

---

## Quick Links

### Current Status

```
Profile          Model                        Gateway      Alias
─────────────────────────────────────────────────────────────────
 ◆default         claude-sonnet-4.5            running      —
  coda            claude-opus-4.6              running      coda
  resa            glm-5.1                     running      resa
  sevi            gpt-5.5                     running      sevi
```

### Key Commands

```bash
# Check status
hermes profile list

# Gateway management
<name> gateway status
systemctl --user restart hermes-gateway-<name>.service

# Test chat
coda chat -q "ping"
resa chat -q "ping"
sevi chat -q "ping"
```

---

## Architecture Overview

```
srv-test01 (Linux)
├── default profile (Kumachii) — Telegram + Discord
│   ├── WOOF (OfficeOps)        ] Light agents
│   ├── CONCRET (ContentOps)    ] via channel_prompts
│   ├── LICA (EduLifeOps)       ]
│   ├── FINA (FinanceOps)       ]
│   └── DOCA (DocOps)           ]
│
├── coda profile — claude-opus-4.6 @ EnowXAI (Discord only)
├── resa profile — glm-5.1 @ opencode-go (Discord only)
└── sevi profile — gpt-5.5 @ openai-codex (Discord only)

Total: 8 agents, 4 gateways, ~621MB RAM
```

---

## Key Learnings

1. **Profile Isolation** — Every profile has its own `auth.json`, `config.yaml`, `.env`, `sessions/`, `memory/`, `skills/`. Credentials are NOT auto-shared.

2. **Token Conflicts** — A bot token can only be held by ONE gateway. Use unique tokens per profile or disable unused platforms.

3. **Empty String vs Unset** — For `.env`, comment out (`# VAR=`) rather than set to empty (`VAR=`).

4. **Model Provider Switching** — When switching providers, clear `base_url` and `api_key` to use defaults from credential pool.

5. **Gateway Status vs Logs** — `journalctl` is the source of truth, not `gateway status`.

---

## File Structure

```
/home/infra/hermes-docs/
├── README.md                      # This file
├── 00-summary.md                  # Executive summary
├── 01-implementation.md          # Implementation guide
├── 02-configuration.md           # Configuration reference
├── 03-architecture.md            # Architecture documentation
└── 04-commissioning-testing.md   # Deployment & testing
```

---

## Troubleshooting

### Gateway won't start
```bash
journalctl --user -u hermes-gateway-<name>.service -l --no-pager
```

### Chat times out
```bash
<name> gateway health
cat ~/.hermes/profiles/<name>/auth.json | python3 -c "import sys,json; d=json.load(sys.stdin); print(list(d.get('credential_pool',{}).keys()))"
```

### Wrong model
```bash
<name> config set model.base_url ""
<name> config set model.api_key ""
systemctl --user restart hermes-gateway-<name>.service
```

---

## Contact

- **User:** Rosinante
- **Assistant:** Kumachii 🤘
- **Platform:** srv-test01 (infra account)
- **Timezone:** Asia/Jakarta (WIB)
