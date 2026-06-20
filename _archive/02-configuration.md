# Hermes Multi-Agent Architecture — Configuration Reference

**Version:** 1.0.0  
**Date:** 2026-05-07  
**Related To:** [00-summary.md](./00-summary.md), [01-implementation.md](./01-implementation.md)

---

## Overview

Dokumen ini berisi semua konfigurasi yang digunakan dalam multi-agent setup — structure config, env vars, credential pool, dan semua settings per profile.

---

## File Structure

```
~/.hermes/
├── auth.json                    # Global credentials (default profile)
├── config.yaml                  # Global/default config
├── .env                         # Global environment variables
├── hermes-agent/                # Hermes Agent source code
│   ├── gateway/
│   ├── agent/
│   └── venv/
├── profiles/
│   ├── coda/
│   │   ├── auth.json           # CODA credentials
│   │   ├── config.yaml         # CODA config
│   │   ├── .env               # CODA env vars
│   │   ├── memory/            # CODA session memory
│   │   ├── sessions/          # CODA conversation history
│   │   └── skills/            # CODA custom skills
│   ├── resa/
│   │   ├── auth.json          # RESA credentials
│   │   ├── config.yaml        # RESA config
│   │   ├── .env               # RESA env vars
│   │   ├── memory/            # RESA session memory
│   │   ├── sessions/          # RESA conversation history
│   │   └── skills/            # RESA custom skills
│   └── sevi/
│       ├── auth.json          # SEVI credentials
│       ├── config.yaml        # SEVI config
│       ├── .env               # SEVI env vars
│       ├── memory/            # SEVI session memory
│       ├── sessions/          # SEVI conversation history
│       └── skills/            # SEVI custom skills
└── cron/                        # Scheduled jobs
```

**Important:** Setiap profile adalah **fully isolated** — punya `auth.json`, `config.yaml`, `.env`, `memory/`, `sessions/`, `skills/` sendiri.

---

## Config Schema (config.yaml)

### Top-Level Structure

```yaml
model:
  provider: <string>           # Provider name (e.g., "custom", "opencode-go", "openai-codex")
  default: <string>           # Model name (e.g., "claude-opus-4.6", "glm-5.1", "gpt-5.5")
  base_url: <string>          # API base URL (set "" untuk default dari credential)
  api_key: <string>           # API key (set "" untuk dari credential pool)

providers: {}                 # Provider configs (rarely used)

fallback_providers: []         # Fallback provider list

credential_pool_strategies: {} # Credential resolution strategy

toolsets:
  - hermes-cli                # Enabled toolsets

agent:
  personalities:              # Available personalities
    catgirl: ...
    concise: ...
    creative: ...
    # etc.
  display:
    personality: concise      # Active personality

channel_prompts:              # Light agents (default profile only)
  - channel_id: "..."
    name: "..."
    description: "..."
    system_prompt: |
      ...

gateway:
  platforms:
    telegram:
      enabled: <bool>         # true untuk default, false untuk dedicated
      bot_token: ${TELEGRAM_BOT_TOKEN}
      allowed_users: ${TELEGRAM_ALLOWED_USERS}
      home_channel: ${TELEGRAM_HOME_CHANNEL}
    
    discord:
      enabled: <bool>
      token: ${DISCORD_BOT_TOKEN}
      home_channel: <string>
      auto_thread: <bool>
```

---

## Environment Variables (.env)

### Global (~/.hermes/.env)

```bash
# Telegram (default profile only)
TELEGRAM_BOT_TOKEN=<TOKEN>
TELEGRAM_ALLOWED_USERS=<USER_IDS>
TELEGRAM_HOME_CHANNEL=<CHANNEL_ID>

# Global defaults
HERMES_DATA_DIR=/home/infra/.hermes
```

### Profile-Specific (~/.hermes/profiles/<name>/.env)

```bash
# CODA
DISCORD_BOT_TOKEN=<TOKEN>
# Telegram commented out (CODA uses Discord only):
# TELEGRAM_BOT_TOKEN=
# TELEGRAM_ALLOWED_USERS=
# TELEGRAM_HOME_CHANNEL=

# RESA
DISCORD_BOT_TOKEN=<TOKEN>
# TELEGRAM_BOT_TOKEN=
# TELEGRAM_ALLOWED_USERS=
# TELEGRAM_HOME_CHANNEL=

# SEVI
DISCORD_BOT_TOKEN=<TOKEN>
# TELEGRAM_BOT_TOKEN=
# TELEGRAM_ALLOWED_USERS=
# TELEGRAM_HOME_CHANNEL=
```

**Note:** Telegram vars di-comment, BUKAN di-set ke empty string. Empty string still triggers fallback logic.

---

## Credential Pool (auth.json)

### Structure

```json
{
  "credential_pool": {
    "<provider_name>": [
      {
        "id": "<credential_id>",
        "label": "<label>",
        "auth_type": "oauth|api-key",
        "priority": 0,
        "source": "<source>",
        "access_token": "<token>",
        "refresh_token": "<refresh_token>",
        "base_url": "<url>",
        "expires_at": "<timestamp>"
      }
    ]
  }
}
```

### Providers & Credentials

| Provider | Auth Type | Base URL | Used By |
|----------|-----------|----------|---------|
| `custom:enowxai` | API Key | http://10.50.0.111:1430/v1 | default, coda |
| `opencode-go` | OAuth | https://opencode.ai/zen/go/v1 | resa |
| `openai-codex` | OAuth | https://chatgpt.com/backend-api/codex | sevi |

### Copying Credentials Between Profiles

```python
import json

def copy_credential(from_profile, to_profile, provider):
    default_path = "/home/infra/.hermes/auth.json"
    target_path = f"/home/infra/.hermes/profiles/{to_profile}/auth.json"
    
    with open(default_path) as f:
        default = json.load(f)
    with open(target_path) as f:
        target = json.load(f)
    
    if "credential_pool" not in target:
        target["credential_pool"] = {}
    
    target["credential_pool"][provider] = default.get("credential_pool", {}).get(provider, [])
    
    with open(target_path, "w") as f:
        json.dump(target, f, indent=2)
    
    print(f"✓ Copied {provider} to {to_profile}")
```

---

## Profile-Specific Configs

### Default Profile (Kumachii)

```yaml
model:
  provider: custom
  default: claude-sonnet-4.5
  base_url: http://10.50.0.111:1430/v1
  api_key: ''

gateway:
  platforms:
    telegram:
      enabled: true
      bot_token: ${TELEGRAM_BOT_TOKEN}
      allowed_users: ${TELEGRAM_ALLOWED_USERS}
      home_channel: ${TELEGRAM_HOME_CHANNEL}
    
    discord:
      enabled: true
      token: ${DISCORD_BOT_TOKEN}
      home_channel: "972903467"
      auto_thread: false

channel_prompts:
  - channel_id: "1501446154955657256"
    name: "WOOF"
    system_prompt: |
      You are WOOF, Office Operations Agent...
  
  - channel_id: "1501446213130649640"
    name: "CONCRET"
    system_prompt: |
      You are CONCRET, Content Operations Agent...
  
  # ... (LICA, FINA, DOCA)
```

### CODA Profile (Coding Agent)

```yaml
model:
  provider: custom
  default: claude-opus-4.6
  base_url: http://10.50.0.111:1430/v1
  api_key: ''

gateway:
  platforms:
    telegram:
      enabled: false
    
    discord:
      enabled: true
      token: ${DISCORD_BOT_TOKEN}
      home_channel: "1501447521086476348"
      auto_thread: false
```

### RESA Profile (Research Agent)

```yaml
model:
  provider: opencode-go
  default: glm-5.1
  base_url: ''
  api_key: ''

gateway:
  platforms:
    telegram:
      enabled: false
    
    discord:
      enabled: true
      token: ${DISCORD_BOT_TOKEN}
      home_channel: "1501447723000266793"
      auto_thread: false
```

### SEVI Profile (Security Agent)

```yaml
model:
  provider: openai-codex
  default: gpt-5.5
  base_url: ''
  api_key: ''

gateway:
  platforms:
    telegram:
      enabled: false
    
    discord:
      enabled: true
      token: ${DISCORD_BOT_TOKEN}
      home_channel: "1501447906396344320"
      auto_thread: false
```

---

## Systemd Services

### Service Files

```
~/.config/systemd/user/
├── hermes-gateway-default.service   # Default profile gateway
├── hermes-gateway-coda.service      # CODA profile gateway
├── hermes-gateway-resa.service     # RESA profile gateway
└── hermes-gateway-sevi.service     # SEVI profile gateway
```

### Service Template

```ini
[Unit]
Description=Hermes Agent Gateway - <Profile Name>
After=network.target

[Service]
Type=simple
User=infra
Environment=PYTHONUNBUFFERED=1
ExecStart=/home/infra/.hermes/hermes-agent/venv/bin/python -m hermes_cli.main --profile <name> gateway run --replace
Restart=always
RestartSec=5
StandardOutput=journal
StandardError=journal

[Install]
WantedBy=default.target
```

### Service Management Commands

```bash
# Start/Stop/Restart
systemctl --user start hermes-gateway-<name>.service
systemctl --user stop hermes-gateway-<name>.service
systemctl --user restart hermes-gateway-<name>.service

# Enable/Disable (auto-start on boot)
systemctl --user enable hermes-gateway-<name>.service
systemctl --user disable hermes-gateway-<name>.service

# Status
systemctl --user status hermes-gateway-<name>.service

# Logs
journalctl --user -u hermes-gateway-<name>.service -f
journalctl --user -u hermes-gateway-<name>.service --since "1 hour ago" --no-pager
```

---

## CLI Commands Reference

### Profile Management

```bash
# List profiles
hermes profile list

# Switch profile (sets default for subsequent commands)
hermes profile switch <name>

# Create new profile
hermes profile create <name> --clone <source>

# Delete profile
hermes profile delete <name>

# Check current profile config
<name> config get <key>
<name> config set <key> <value>
<name> config list
```

### Gateway Management

```bash
# Install gateway service
<name> gateway install

# Start/stop gateway
systemctl --user start hermes-gateway-<name>.service
systemctl --user stop hermes-gateway-<name>.service

# Gateway status
<name> gateway status

# Gateway health
<name> gateway health
```

### Chat Testing

```bash
# Interactive chat
<name> chat

# Single query
<name> chat -q "your question"

# Verbose (show all logs)
<name> chat -q "ping" -v
```

### Credential Management

```bash
# List all credentials
hermes auth list

# Add credential
<name> auth add <provider> --type oauth|api-key

# Remove credential
<name> auth remove <provider>
```

---

## Key Learnings

### 1. Profile Isolation
- Every profile has its own `auth.json`, `config.yaml`, `.env`, `sessions/`, `memory/`, `skills/`
- Credentials are NOT shared automatically between profiles
- Must copy credentials manually from `~/.hermes/auth.json` to each profile's `auth.json`

### 2. Token Conflicts
- A bot token can only be held by ONE gateway at a time
- If multiple profiles use the same Telegram/Discord token, only the first one will start
- Solution: Use unique tokens per profile, or disable the platform in non-primary profiles

### 3. Empty String vs Unset
- For `.env` variables: Comment out (`# VAR=`) rather than set to empty (`VAR=`)
- Empty string may still trigger fallback logic in some cases
- Setting to empty can cause unexpected behavior with boolean checks

### 4. Model Provider Switching
- When switching providers (e.g., from `custom` to `opencode-go`), clear `base_url` and `api_key`
- Otherwise, the old `base_url` may still be used, causing 401 auth errors
- Use `config set model.base_url ""` and `config set model.api_key ""`

### 5. Gateway Status vs Logs
- `gateway status` may show stale/warning information
- `journalctl` logs are the source of truth
- Always check logs when debugging gateway issues
