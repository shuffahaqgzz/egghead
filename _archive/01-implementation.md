# Hermes Multi-Agent Architecture — Implementation

**Version:** 1.0.0  
**Date:** 2026-05-07  
**Related To:** [00-summary.md](./00-summary.md)

---

## Overview

Dokumen ini menjelaskan implementasi teknis multi-agent architecture — mulai dari discovery, troubleshooting, sampai final configuration.

---

## Phase 1: Light Agents (Channel Prompts)

Light agents tidak butuh profile terpisah. Mereka jalan di dalam default profile via `channel_prompts` configuration.

### Setup

Tambah di `~/.hermes/config.yaml`:

```yaml
channel_prompts:
  - channel_id: "1501446154955657256"
    name: "WOOF"
    description: "Office Operations Agent"
    system_prompt: |
      You are WOOF, Office Operations Agent...
  
  - channel_id: "1501446213130649640"
    name: "CONCRET"
    description: "Content Operations Agent"
    system_prompt: |
      You are CONCRET, Content Operations Agent...
  
  # ... (LICA, FINA, DOCA)
```

### Agents

| Agent | Channel ID | Role |
|-------|------------|------|
| WOOF | 1501446154955657256 | Office Operations |
| CONCRET | 1501446213130649640 | Content Operations |
| LICA | 1501446282894512228 | Education/Life Operations |
| FINA | 1501446306512502845 | Finance Operations |
| DOCA | 1501446326523527239 | Document Operations |

### Resource Usage

Zero additional RAM — berjalan di dalam default gateway process.

---

## Phase 2: Dedicated Profile Agents

3 profile agents dengan dedicated bot token, channel, dan model.

### Step 1: Create Profiles

```bash
hermes profile create coda --clone default
hermes profile create resa --clone default
hermes profile create sevi --clone default
```

### Step 2: Configure Discord Bot (per profile)

```bash
# CODA
coda config set discord.token "MTUwMTYwODk4OTY1NjQ4MjAwMw..."
coda config set discord.home_channel "1501447521086476348"
coda config set discord.auto_thread false

# RESA
resa config set discord.token "MTUwMTYwODg3NDEzMjg5NzgwMg..."
resa config set discord.home_channel "1501447723000266793"
resa config set discord.auto_thread false

# SEVI
sevi config set discord.token "MTUwMTYwOTA3NjUwMzc0MDQ2Ng..."
sevi config set discord.home_channel "1501447906396344320"
sevi config set discord.auto_thread false
```

### Step 3: Disable Telegram (prevent token conflict)

```bash
# Disable Telegram
coda config set telegram.enabled false
resa config set telegram.enabled false
sevi config set telegram.enabled false

# Comment out Telegram vars di .env (bukan set empty string!)
# Di ~/.hermes/profiles/<name>/.env:
# TELEGRAM_BOT_TOKEN=
# TELEGRAM_ALLOWED_USERS=
# TELEGRAM_HOME_CHANNEL=
```

**Important:** Comment out, jangan set ke empty string. Empty string masih trigger fallback logic yang bisa cause conflict.

### Step 4: Set Model & Provider

```bash
# RESA - Research Agent
resa config set model.provider opencode-go
resa config set model.default glm-5.1
resa config set model.base_url ""
resa config set model.api_key ""

# SEVI - Security Agent
sevi config set model.provider openai-codex
sevi config set model.default gpt-5.5
sevi config set model.base_url ""
sevi config set model.api_key ""

# CODA - Coding Agent (pake EnowXAI seperti default)
coda config set model.provider custom
coda config set model.default claude-opus-4.6
coda config set model.base_url http://10.50.0.111:1430/v1
coda config set model.api_key ""
```

### Step 5: Copy Credentials (CRITICAL!)

Profiles pakai `auth.json` terpisah. Credentials tidak auto-shared.

```python
#!/usr/bin/env python3
"""Copy credentials from default profile to dedicated profiles."""

import json
import os

DEFAULT_AUTH = "/home/infra/.hermes/auth.json"
PROFILES = ["coda", "resa", "sevi"]

# Credentials to copy per profile
CREDENTIALS_MAP = {
    "coda": ["custom:enowxai"],
    "resa": ["opencode-go"],
    "sevi": ["openai-codex"],
}

def copy_credentials():
    with open(DEFAULT_AUTH) as f:
        default_data = json.load(f)
    
    default_pool = default_data.get("credential_pool", {})
    
    for profile in PROFILES:
        profile_auth_path = f"/home/infra/.hermes/profiles/{profile}/auth.json"
        
        with open(profile_auth_path) as f:
            profile_data = json.load(f)
        
        if "credential_pool" not in profile_data:
            profile_data["credential_pool"] = {}
        
        for cred_provider in CREDENTIALS_MAP.get(profile, []):
            if cred_provider in default_pool:
                profile_data["credential_pool"][cred_provider] = default_pool[cred_provider]
                print(f"✓ Copied {cred_provider} to {profile}")
        
        with open(profile_auth_path, "w") as f:
            json.dump(profile_data, f, indent=2)
    
    print("Done!")

if __name__ == "__main__":
    copy_credentials()
```

### Step 6: Install & Start Gateways

```bash
# Install service
coda gateway install
resa gateway install
sevi gateway install

# Start services
systemctl --user start hermes-gateway-coda.service
systemctl --user start hermes-gateway-resa.service
systemctl --user start hermes-gateway-sevi.service

# Verify
hermes profile list
```

---

## Troubleshooting Log

### Issue 1: Gateway Crash Loop — "Telegram bot token already in use"

**Symptom:**
```
WARNING: Telegram bot token already in use (PID 2921994). Stop the other gateway first.
```

**Root Cause:** Default profile gateway sudah pegang Telegram token. Dedicated profiles coba pegang token yang sama.

**Solution:** Disable Telegram di dedicated profiles (see Step 3 above).

---

### Issue 2: Model Not Changing — Still Using Default Model

**Symptom:** RESA/RESA masih pake `claude-opus-4.6` padahal udah di-set ke `glm-5.1`.

**Root Cause:** `base_url` masih pointing ke EnowXAI, causing 401 auth error dan fallback.

**Solution:** Clear `base_url` dan `api_key`:
```bash
resa config set model.base_url ""
resa config set model.api_key ""
```

---

### Issue 3: "No Codex OAuth token found" / "No opencode-go credentials"

**Symptom:**
```
WARNING - resolve_provider_client: openai-codex requested but no Codex OAuth token found
```

**Root Cause:** Profiles pakai `auth.json` terpisah. Credentials tidak auto-shared dari `~/.hermes/auth.json`.

**Solution:** Copy credentials manual (see Step 5 above).

---

### Issue 4: 401 Authentication Error with Valid Credential

**Symptom:**
```
API call failed: AuthenticationError [HTTP 401]
```

**Root Cause:** `api_key` atau `base_url` masih ter-set dari profile clone, pointing ke wrong provider.

**Solution:** Clear both:
```bash
<name> config set model.api_key ""
<name> config set model.base_url ""
```

---

## Verification Commands

```bash
# Check all profiles
hermes profile list

# Check specific gateway status
coda gateway status
resa gateway status
sevi gateway status

# Check logs
journalctl --user -u hermes-gateway-coda --since "10 minutes ago" --no-pager
journalctl --user -u hermes-gateway-resa --since "10 minutes ago" --no-pager
journalctl --user -u hermes-gateway-sevi --since "10 minutes ago" --no-pager

# Test chat
coda chat -q "ping"
resa chat -q "ping"
sevi chat -q "ping"

# Verify model
coda chat -q "what model are you using?"
resa chat -q "what model are you using?"
sevi chat -q "what model are you using?"

# Verify credentials
cat ~/.hermes/profiles/<name>/auth.json | python3 -c "import sys,json; d=json.load(sys.stdin); print(list(d.get('credential_pool',{}).keys()))"
```

---

## Rollback Procedures

### Disable a profile agent:
```bash
systemctl --user stop hermes-gateway-<name>.service
systemctl --user disable hermes-gateway-<name>.service
```

### Remove a profile:
```bash
hermes profile delete <name>
rm -rf ~/.hermes/profiles/<name>
```

### Reset model to default:
```bash
<name> config set model.provider custom
<name> config set model.default claude-opus-4.6
<name> config set model.base_url http://10.50.0.111:1430/v1
systemctl --user restart hermes-gateway-<name>.service
```
