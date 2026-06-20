# Hermes Multi-Agent Architecture — Commissioning & Testing

**Version:** 1.0.0  
**Date:** 2026-05-07  
**Related To:** [00-summary.md](./00-summary.md), [01-implementation.md](./01-implementation.md), [02-configuration.md](./02-configuration.md)

---

## Overview

Dokumen ini berisi prosedur commissioning (deployment) dan testing untuk memverifikasi semua komponen berjalan dengan benar.

---

## Pre-Commissioning Checklist

### Infrastructure

- [x] Server accessible (srv-test01)
- [x] User account ready (infra)
- [x] SSH access configured
- [x] Python 3.11+ available
- [x] Systemd available
- [x] Network connectivity (for LLM providers)

### Dependencies

- [x] Hermes Agent installed (`~/.hermes/hermes-agent/`)
- [x] Virtual environment ready (`venv/`)
- [x] Hermes CLI available (`hermes` command)

### Configuration

- [x] Global config ready (`~/.hermes/config.yaml`)
- [x] Global credentials ready (`~/.hermes/auth.json`)
- [x] Global env vars ready (`~/.hermes/.env`)
- [x] All profiles created (`default`, `coda`, `resa`, `sevi`)
- [x] Per-profile configs set
- [x] Per-profile credentials copied
- [x] Per-profile env vars set

### Platform Tokens

- [x] Telegram bot token (default only)
- [x] Discord bot tokens (one per profile: coda, resa, sevi)
- [x] Discord channel IDs ready
- [x] All tokens validated (not revoked)

---

## Commissioning Procedures

### Step 1: Verify Base System

```bash
# Check Hermes installation
hermes --version
hermes profile list

# Expected output:
# Profile          Model                        Gateway      Alias
#  ◆default       claude-sonnet-4.5            stopped      —
#   coda          claude-opus-4.6             stopped      coda
#   resa          glm-5.1                     stopped      resa
#   sevi          gpt-5.5                     stopped      sevi
```

### Step 2: Install Gateway Services

For each new profile (coda, resa, sevi):

```bash
# Login to profile's auth (if needed)
<name> auth add <provider> --type oauth

# Install systemd service
<name> gateway install

# Enable auto-start
systemctl --user enable hermes-gateway-<name>.service
```

### Step 3: Start All Gateways

```bash
# Start default gateway first
systemctl --user start hermes-gateway-default.service
sleep 3

# Start dedicated gateways
systemctl --user start hermes-gateway-coda.service
systemctl --user start hermes-gateway-resa.service
systemctl --user start hermes-gateway-sevi.service
sleep 3

# Verify
hermes profile list
```

### Step 4: Verify Gateway Status

```bash
# Check all gateways
hermes profile list

# Expected: all gateways "running"
```

### Step 5: Verify Credentials

```bash
# Per-profile credential check
for profile in coda resa sevi; do
  echo "=== $profile ==="
  cat ~/.hermes/profiles/$profile/auth.json | python3 -c "import sys,json; d=json.load(sys.stdin); print(list(d.get('credential_pool',{}).keys()))"
done

# Expected:
# === coda ===
# ['custom:enowxai']
# === resa ===
# ['opencode-go']
# === sevi ===
# ['openai-codex']
```

---

## Testing Procedures

### Unit Tests: Gateway Startup

#### Test 1.1: Default Gateway Startup

```bash
systemctl --user status hermes-gateway-default.service
```

**Expected:**
```
● hermes-gateway-default.service - Hermes Agent Gateway - Default Profile
   Loaded: loaded (/home/infra/.config/systemd/user/...); enabled
   Active: active (running) since Thu 2026-05-07 HH:MM:SS WIB
   Main PID: XXXXX (python)
```

#### Test 1.2: Dedicated Gateway Startup

```bash
systemctl --user status hermes-gateway-coda.service
systemctl --user status hermes-gateway-resa.service
systemctl --user status hermes-gateway-sevi.service
```

**Expected:** All show `Active: active (running)`

#### Test 1.3: Gateway Auto-Restart

```bash
# Force kill a gateway
kill -9 $(pgrep -f "hermes.*gateway.*coda")

# Wait 10 seconds
sleep 10

# Check if it restarted
systemctl --user status hermes-gateway-coda.service
```

**Expected:** `Active: active (running)` — systemd auto-restarted it

---

### Unit Tests: Model Configuration

#### Test 2.1: Verify Model Per Profile

```bash
# CODA
head -6 ~/.hermes/profiles/coda/config.yaml | grep -E "provider|default"
# Expected: provider: custom, default: claude-opus-4.6

# RESA
head -6 ~/.hermes/profiles/resa/config.yaml | grep -E "provider|default"
# Expected: provider: opencode-go, default: glm-5.1

# SEVI
head -6 ~/.hermes/profiles/sevi/config.yaml | grep -E "provider|default"
# Expected: provider: openai-codex, default: gpt-5.5
```

#### Test 2.2: Verify Base URL Empty

```bash
# RESA and SEVI should have empty base_url
grep "base_url" ~/.hermes/profiles/resa/config.yaml
grep "base_url" ~/.hermes/profiles/sevi/config.yaml
# Expected: base_url: '' or no match (empty)
```

---

### Integration Tests: Chat

#### Test 3.1: Default Profile Chat

```bash
timeout 30 hermes chat -q "ping" 2>&1 | tail -5
```

**Expected:** Response with "pong" or similar

#### Test 3.2: CODA Chat

```bash
timeout 30 coda chat -q "ping - respond with 'CODA online'" 2>&1 | grep "CODA"
```

**Expected:** "CODA online"

#### Test 3.3: RESA Chat

```bash
timeout 30 resa chat -q "ping - respond with 'RESA online'" 2>&1 | grep "RESA"
```

**Expected:** "RESA online"

#### Test 3.4: SEVI Chat

```bash
timeout 30 sevi chat -q "ping - respond with 'SEVI online'" 2>&1 | grep "SEVI"
```

**Expected:** "SEVI online"

---

### Integration Tests: Model Verification

#### Test 4.1: Verify CODA Model

```bash
timeout 30 coda chat -q "what model are you using? respond with just the model name" 2>&1 | grep -E "claude-opus|claude-opus-4"
```

**Expected:** "claude-opus-4.6"

#### Test 4.2: Verify RESA Model

```bash
timeout 30 resa chat -q "what model are you using? respond with just the model name" 2>&1 | grep "glm-5"
```

**Expected:** "glm-5.1"

#### Test 4.3: Verify SEVI Model

```bash
timeout 30 sevi chat -q "what model are you using? respond with just the model name" 2>&1 | grep "gpt-5"
```

**Expected:** "gpt-5.5"

---

### Integration Tests: Platform Access

#### Test 5.1: Telegram (Default Only)

```bash
# Send message via Telegram bot
# Expected: Bot responds in DM

# Verify other profiles DON'T have Telegram
grep "telegram" ~/.hermes/profiles/coda/config.yaml -A 2
grep "telegram" ~/.hermes/profiles/resa/config.yaml -A 2
grep "telegram" ~/.hermes/profiles/sevi/config.yaml -A 2
# Expected: all show enabled: false
```

#### Test 5.2: Discord (All Profiles)

```bash
# For each profile, verify Discord enabled
grep "discord" ~/.hermes/profiles/coda/config.yaml -A 3
# Expected: enabled: true

# Discord channels accessible?
# (Manual test: send message in each channel)
```

---

### Integration Tests: Light Agents

#### Test 6.1: WOOF Agent

```bash
# In Discord channel 1501446154955657256 (WOOF channel)
# Mention bot or send command
# Expected: WOOF responds with OfficeOps specialist behavior
```

#### Test 6.2: CONCRET Agent

```bash
# In Discord channel 1501446213130649640 (CONCRET channel)
# Expected: CONCRET responds as ContentOps specialist
```

#### Test 6.3: LICA Agent

```bash
# In Discord channel 1501446282894512228 (LICA channel)
# Expected: LICA responds as EduLifeOps specialist
```

#### Test 6.4: FINA Agent

```bash
# In Discord channel 1501446306512502845 (FINA channel)
# Expected: FINA responds as FinanceOps specialist
```

#### Test 6.5: DOCA Agent

```bash
# In Discord channel 1501446326523527239 (DOCA channel)
# Expected: DOCA responds as DocOps specialist
```

---

### Stress Tests: Concurrent Access

#### Test 7.1: All Profiles Chat Simultaneously

```bash
# Run all in parallel
coda chat -q "ping" &
resa chat -q "ping" &
sevi chat -q "ping" &
wait

# Check all completed without error
# Expected: All respond, no token conflicts
```

#### Test 7.2: Rapid Fire Messages

```bash
# Send 10 messages rapidly to same agent
for i in {1..10}; do
  echo "Message $i"
  timeout 10 resa chat -q "message $i" 2>&1 | tail -1
done
# Expected: All processed, no crashes
```

---

### Security Tests

#### Test 8.1: Profile Isolation

```bash
# Profile A should not access Profile B's sessions
ls ~/.hermes/profiles/coda/sessions/ | wc -l
ls ~/.hermes/profiles/resa/sessions/ | wc -l
ls ~/.hermes/profiles/sevi/sessions/ | wc -l

# Each should have separate session files
```

#### Test 8.2: Credential Isolation

```bash
# Try to access coda's credentials from resa
# (Should fail — profiles are isolated)

# Verify no cross-profile credential leakage
cat ~/.hermes/profiles/resa/auth.json | grep "enowxai"
# Expected: no output (coda's credential not in resa)
```

---

## Post-Commissioning Verification

### Final Status Check

```bash
hermes profile list
```

**Expected Output:**
```
Profile          Model                        Gateway      Alias
────────────────────────────────────────────────────────────────
 ◆default         claude-sonnet-4.5            running      —
  coda            claude-opus-4.6              running      coda
  resa            glm-5.1                     running      resa
  sevi            gpt-5.5                     running      sevi
```

### Resource Check

```bash
ps aux | grep -E "hermes.*gateway" | grep -v grep
```

**Expected:** 4 processes running

### Log Check

```bash
journalctl --user -u hermes-gateway-default --since "5 minutes ago" --no-pager | grep -i error
journalctl --user -u hermes-gateway-coda --since "5 minutes ago" --no-pager | grep -i error
journalctl --user -u hermes-gateway-resa --since "5 minutes ago" --no-pager | grep -i error
journalctl --user -u hermes-gateway-sevi --since "5 minutes ago" --no-pager | grep -i error
```

**Expected:** No error lines

---

## Troubleshooting

### Gateway Won't Start

```bash
# Check detailed logs
journalctl --user -u hermes-gateway-<name>.service -l --no-pager

# Common issues:
# - Token conflict: Check if another gateway using same token
# - Port conflict: Check if port already in use
# - Auth issue: Verify credentials exist
```

### Chat Times Out

```bash
# Check if gateway is responsive
<name> gateway health

# Check model configuration
<name> config get model.provider
<name> config get model.default

# Check credentials
cat ~/.hermes/profiles/<name>/auth.json | python3 -c "import sys,json; d=json.load(sys.stdin); print(list(d.get('credential_pool',{}).keys()))"
```

### Wrong Model Response

```bash
# Verify config
<name> config get model.provider
<name> config get model.default
<name> config get model.base_url

# Restart gateway after config change
systemctl --user restart hermes-gateway-<name>.service
```

---

## Rollback Procedures

### Full Rollback (All Profiles)

```bash
# Stop all gateways
systemctl --user stop hermes-gateway-default.service
systemctl --user stop hermes-gateway-coda.service
systemctl --user stop hermes-gateway-resa.service
systemctl --user stop hermes-gateway-sevi.service

# Disable all services
systemctl --user disable hermes-gateway-coda.service
systemctl --user disable hermes-gateway-resa.service
systemctl --user disable hermes-gateway-sevi.service

# (Keep default profile running)
systemctl --user start hermes-gateway-default.service
```

### Single Profile Rollback

```bash
# Stop specific gateway
systemctl --user stop hermes-gateway-<name>.service

# Revert config changes
# (Restore from backup or manually reset)

# Restart
systemctl --user start hermes-gateway-<name>.service
```

---

## Sign-Off

### Commissioning Completed

| Component | Status | Date | By |
|-----------|--------|------|-----|
| Default Profile | ✅ Complete | 2026-05-07 | Rosinante |
| CODA Profile | ✅ Complete | 2026-05-07 | Rosinante |
| RESA Profile | ✅ Complete | 2026-05-07 | Rosinante |
| SEVI Profile | ✅ Complete | 2026-05-07 | Rosinante |
| Light Agents | ✅ Complete | 2026-05-07 | Rosinante |

### Testing Passed

| Test Suite | Status | Date | By |
|-------------|--------|------|-----|
| Gateway Startup | ✅ Passed | 2026-05-07 | Rosinante |
| Model Configuration | ✅ Passed | 2026-05-07 | Rosinante |
| Chat Integration | ✅ Passed | 2026-05-07 | Rosinante |
| Platform Access | ✅ Passed | 2026-05-07 | Rosinante |
| Light Agents | ✅ Passed | 2026-05-07 | Rosinante |
| Security Isolation | ✅ Passed | 2026-05-07 | Rosinante |

---

## Appendix: Quick Test Commands

```bash
# One-liner comprehensive test
hermes profile list && echo "=== Testing Chat ===" && \
timeout 15 coda chat -q "ping" 2>&1 | tail -2 && \
timeout 15 resa chat -q "ping" 2>&1 | tail -2 && \
timeout 15 sevi chat -q "ping" 2>&1 | tail -2 && \
echo "=== All Tests Complete ==="
```
