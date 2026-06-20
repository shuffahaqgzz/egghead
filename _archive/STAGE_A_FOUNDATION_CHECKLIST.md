# Stage A Foundation Checklist — Phase 5.1

**Version:** 1.0.0
**Date:** 2026-05-13
**Phase:** 5.0 Conceptual Fix — step 7/9
**Purpose:** Executable checklist untuk Phase 5.1 (Stage A Foundation). 2 profile (KUMACHII + STELLA) + systemd + isolation + handoff test.
**Estimated duration:** 2-3 hari
**Pre-requisite:** Phase 5.0 complete (semua dokumen plan ini tersedia)

---

## 0. Pre-Flight (sebelum mulai Stage A)

- [ ] `PHASE_5_0_CONCEPTUAL_FIX_PLAN.md` dibaca & approved Gilang
- [ ] `SPEC_AUDIT.md` dibaca
- [ ] `MIGRATION_MAPPING.md` dibaca
- [ ] Backup old specs: `mv ~/.hermes/specs/ ~/.hermes/specs-legacy-2026-05/`
- [ ] Git tag existing projects:
  ```bash
  cd /home/infra/projects/stella-agent && git tag pre-framework-v1.1.0-migration
  cd /home/infra/projects/kumachii-executor && git tag pre-framework-v1.1.0-migration
  ```
- [ ] Identify Telegram bot token (currently used by Hermes default gateway) — will be claimed by KUMACHII
- [ ] Confirm `hermes profile create` command works: `hermes profile create --help`
- [ ] Confirm systemd user services available: `systemctl --user status`
- [ ] Confirm loginctl linger enabled: `loginctl show-user infra | grep Linger`

---

## 1. Create KUMACHII Profile

### 1.1 Profile Creation

```bash
hermes profile create kumachii
```

### 1.2 Model Configuration

```bash
# Balanced model for personal assistant (multilingual, summarization)
# Use current 9Router auto-thinking as default (routes to Haiku 4.5 for simple, Opus for complex)
hermes --profile kumachii config set model.default "auto-thinking"
hermes --profile kumachii config set model.provider "custom"
```

Alternative if dedicated model preferred:
```bash
hermes --profile kumachii config set model.default "anthropic/claude-haiku-4.5"
hermes --profile kumachii config set model.provider "openrouter"
```

### 1.3 Platform Configuration (Telegram)

```bash
# KUMACHII owns Telegram exclusively (Section 12.2)
# Copy existing Telegram bot token to kumachii profile .env
# Comment out Telegram vars in default profile .env

# In ~/.hermes/profiles/kumachii/.env:
TELEGRAM_BOT_TOKEN=<existing_token>
TELEGRAM_ALLOWED_USERS=972903467
TELEGRAM_HOME_CHANNEL=972903467
```

### 1.4 SOUL.md Installation

```bash
cp ~/.hermes/knowledge/topics/framework-v1.1.0/soul-drafts/KUMACHII-SOUL.md \
   ~/.hermes/profiles/kumachii/soul.md
```

Or set via config:
```bash
hermes --profile kumachii config set agent.soul_path "soul.md"
```

### 1.5 Memory Initialization

```bash
# Kumachii memory scope: personal preferences
# Copy relevant user profile data
mkdir -p ~/.hermes/profiles/kumachii/memory/
```

### 1.6 Verification

- [ ] `hermes --profile kumachii chat -q "ping — respond with KUMACHII online"` → responds correctly
- [ ] Profile directory exists: `ls ~/.hermes/profiles/kumachii/`
- [ ] Config exists: `cat ~/.hermes/profiles/kumachii/config.yaml`
- [ ] Auth exists: `cat ~/.hermes/profiles/kumachii/auth.json`

---

## 2. Create STELLA Profile

### 2.1 Profile Creation

```bash
hermes profile create stella
```

### 2.2 Model Configuration

```bash
# Strongest reasoning for orchestration (Opus-class)
hermes --profile stella config set model.default "anthropic/claude-opus-4"
hermes --profile stella config set model.provider "openrouter"
```

Or via 9Router:
```bash
hermes --profile stella config set model.default "auto-thinking"
hermes --profile stella config set model.provider "custom"
```

### 2.3 Platform Configuration (Discord)

```bash
# STELLA uses Discord (Section 12.2)
# Requires unique Discord bot token — NOT the same as default profile

# In ~/.hermes/profiles/stella/.env:
DISCORD_BOT_TOKEN=<stella_discord_token>
DISCORD_HOME_CHANNEL=<stella_channel_id>

# Comment out Telegram (STELLA does not use Telegram):
# TELEGRAM_BOT_TOKEN=
# TELEGRAM_ALLOWED_USERS=
# TELEGRAM_HOME_CHANNEL=
```

**Note:** If Discord bot not yet created, STELLA can initially run CLI-only (no gateway) for testing. Discord setup can be deferred to end of Stage A.

### 2.4 SOUL.md Installation

```bash
cp ~/.hermes/knowledge/topics/framework-v1.1.0/soul-drafts/STELLA-SOUL.md \
   ~/.hermes/profiles/stella/soul.md
```

### 2.5 Memory Initialization

```bash
# Stella memory scope: workflow state
mkdir -p ~/.hermes/profiles/stella/memory/
```

### 2.6 Verification

- [ ] `hermes --profile stella chat -q "ping — respond with STELLA online"` → responds correctly
- [ ] Profile directory exists: `ls ~/.hermes/profiles/stella/`
- [ ] Config exists: `cat ~/.hermes/profiles/stella/config.yaml`
- [ ] Auth exists: `cat ~/.hermes/profiles/stella/auth.json`

---

## 3. Systemd Services

### 3.1 Create Service Files

```bash
# KUMACHII gateway service
cat > ~/.config/systemd/user/hermes-gateway-kumachii.service << 'EOF'
[Unit]
Description=Hermes Agent Gateway - kumachii
After=network.target

[Service]
Type=simple
Environment=PYTHONUNBUFFERED=1
ExecStart=/home/infra/.hermes/hermes-agent/venv/bin/python -m hermes_cli.main --profile kumachii gateway run --replace
Restart=always
RestartSec=5
StandardOutput=journal
StandardError=journal

[Install]
WantedBy=default.target
EOF

# STELLA gateway service
cat > ~/.config/systemd/user/hermes-gateway-stella.service << 'EOF'
[Unit]
Description=Hermes Agent Gateway - stella
After=network.target

[Service]
Type=simple
Environment=PYTHONUNBUFFERED=1
ExecStart=/home/infra/.hermes/hermes-agent/venv/bin/python -m hermes_cli.main --profile stella gateway run --replace
Restart=always
RestartSec=5
StandardOutput=journal
StandardError=journal

[Install]
WantedBy=default.target
EOF
```

### 3.2 Enable & Start

```bash
systemctl --user daemon-reload
systemctl --user enable hermes-gateway-kumachii.service
systemctl --user enable hermes-gateway-stella.service
systemctl --user start hermes-gateway-kumachii.service
systemctl --user start hermes-gateway-stella.service
```

### 3.3 Verification

- [ ] `systemctl --user status hermes-gateway-kumachii.service` → `Active: active (running)`
- [ ] `systemctl --user status hermes-gateway-stella.service` → `Active: active (running)`
- [ ] No errors in journal: `journalctl --user -u hermes-gateway-kumachii.service --since "5 min ago" --no-pager | grep -i error`
- [ ] No errors in journal: `journalctl --user -u hermes-gateway-stella.service --since "5 min ago" --no-pager | grep -i error`

---

## 4. Isolation Tests (Framework Section 24)

### 4.1 Credential Isolation

```bash
for profile in kumachii stella; do
  echo "=== $profile ==="
  cat ~/.hermes/profiles/$profile/auth.json | python3 -c \
    "import sys,json; d=json.load(sys.stdin); print(list(d.get('credential_pool',{}).keys()))"
done
```

**Expected:** Each profile shows its own credentials, no overlap of bot tokens.

- [ ] KUMACHII has Telegram token, STELLA does not
- [ ] STELLA has Discord token (or empty if deferred), KUMACHII does not have STELLA's Discord token
- [ ] No shared bot tokens between profiles

### 4.2 Token Conflict Test

```bash
journalctl --user --since "10 minutes ago" | grep -i "token already in use"
```

**Expected:** No output (no token conflicts).

- [ ] No "token already in use" errors

### 4.3 Chat Independence Test

```bash
hermes --profile kumachii chat -q "What is your name and role?"
hermes --profile stella chat -q "What is your name and role?"
```

**Expected:**
- KUMACHII identifies as personal assistant
- STELLA identifies as orchestrator

- [ ] KUMACHII responds with correct identity
- [ ] STELLA responds with correct identity
- [ ] Responses are different (not same generic response)

### 4.4 Memory Isolation Test

```bash
# Write to kumachii memory
hermes --profile kumachii chat -q "Remember: my favorite color is blue"

# Check stella doesn't have it
hermes --profile stella chat -q "What is the user's favorite color?"
```

**Expected:** STELLA should not know the answer (memory isolated).

- [ ] KUMACHII remembers
- [ ] STELLA does not know

---

## 5. Handoff Test (KUMACHII → STELLA)

### 5.1 Prepare Handoff Template

Create test handoff file:

```bash
mkdir -p /tmp/handoff-test/
cat > /tmp/handoff-test/handoff-001.md << 'EOF'
# Agent Handoff

From: KUMACHII
To: STELLA
Task ID: TEST-001
Phase: discovery
Priority: low
Objective: Test handoff protocol between KUMACHII and STELLA

Inputs:
- User request: "Plan a simple personal finance tracker"

Files touched:
- (none yet)

Decisions made:
- Classified as project-level work (not personal task)

Assumptions:
- Single user (personal use)
- Web-based

Constraints:
- No paid dependencies
- Must work offline

Open risks:
- None identified yet

Acceptance criteria:
- STELLA acknowledges receipt
- STELLA identifies next phase (Planning)

Required review:
- User (Gilang)

Next action:
- STELLA opens Phase 1 (Planning)

Status: PASS
EOF
```

### 5.2 Test STELLA Receives Handoff

```bash
hermes --profile stella chat -q "$(cat /tmp/handoff-test/handoff-001.md)

Read the above handoff document. Acknowledge receipt, identify the current phase, and state what you would do next as orchestrator."
```

**Expected:** STELLA acknowledges, identifies Phase 0 → 1 transition, proposes creating PRD.

- [ ] STELLA acknowledges handoff
- [ ] STELLA identifies correct phase
- [ ] STELLA proposes appropriate next action
- [ ] Response follows STELLA SOUL.md behavior (terse, structured)

### 5.3 Test KUMACHII Intake → Handoff Generation

```bash
hermes --profile kumachii chat -q "I want to build a personal finance dashboard that tracks expenses and generates monthly reports. This is a project, not a quick task. Generate a handoff document for STELLA using the markdown handoff template."
```

**Expected:** KUMACHII generates properly formatted handoff markdown.

- [ ] Output contains all mandatory handoff fields
- [ ] From: KUMACHII, To: STELLA
- [ ] Phase: discovery
- [ ] Objective is clear
- [ ] Constraints and assumptions listed

---

## 6. Default Profile Adjustment

After KUMACHII claims Telegram:

### 6.1 Stop Default Gateway (if running with Telegram)

```bash
# Check if default gateway is using Telegram
hermes gateway status

# If yes, stop it before starting kumachii gateway
# (to avoid token conflict)
systemctl --user stop hermes-gateway.service
```

### 6.2 Comment Out Telegram in Default Profile

```bash
# In ~/.hermes/.env, comment out Telegram vars:
# TELEGRAM_BOT_TOKEN=...
# TELEGRAM_ALLOWED_USERS=...
# TELEGRAM_HOME_CHANNEL=...
```

**Important:** The default profile (Hermes main) can keep running for CLI usage. Only the gateway needs to not conflict on Telegram token.

- [ ] Default profile gateway stopped or Telegram vars removed
- [ ] No token conflict between default and kumachii

---

## 7. Exit Criteria — Stage A Complete

All items below must be checked before declaring Stage A done:

### Infrastructure
- [ ] 2 profiles created (`kumachii`, `stella`)
- [ ] 2 systemd services running
- [ ] Restart policy active (services auto-restart on crash)
- [ ] Logs clean (no errors in last 30 min)

### Isolation
- [ ] Credential isolation verified (Section 4.1)
- [ ] No token conflicts (Section 4.2)
- [ ] Chat independence verified (Section 4.3)
- [ ] Memory isolation verified (Section 4.4)

### Functionality
- [ ] KUMACHII responds on Telegram
- [ ] STELLA responds on Discord (or CLI if Discord deferred)
- [ ] Handoff markdown generated correctly by KUMACHII (Section 5.3)
- [ ] Handoff received and processed by STELLA (Section 5.2)

### Scalability Proof
- [ ] Can add 3rd profile without touching KUMACHII or STELLA config:
  ```bash
  hermes profile create test-agent
  hermes --profile test-agent chat -q "ping"
  hermes profile delete test-agent
  ```

### Documentation
- [ ] Stage A completion noted in `~/.hermes/knowledge/topics/framework-v1.1.0/STAGE_A_STATUS.md`

---

## 8. Rollback Plan

If Stage A fails critically:

```bash
# Stop new services
systemctl --user stop hermes-gateway-kumachii.service
systemctl --user stop hermes-gateway-stella.service
systemctl --user disable hermes-gateway-kumachii.service
systemctl --user disable hermes-gateway-stella.service

# Restore Telegram to default profile
# Uncomment TELEGRAM_* vars in ~/.hermes/.env

# Restart default gateway
systemctl --user start hermes-gateway.service

# Profiles can be deleted:
hermes profile delete kumachii
hermes profile delete stella
```

Time to rollback: < 5 minutes.

---

## 9. Known Blockers & Decisions Needed from Gilang

Before starting Stage A execution:

1. **Telegram bot token:** Confirm the existing token (`~/.hermes/.env` → `TELEGRAM_BOT_TOKEN`) will be moved to KUMACHII profile. Default profile loses Telegram access.

2. **Discord for STELLA:** Do we create a new Discord bot now, or defer Discord and test STELLA CLI-only first?
   - Option (a): Create Discord bot now → full Stage A
   - Option (b): Defer Discord, STELLA CLI-only → faster start, Discord in Stage A.5

3. **Model for STELLA:** Use 9Router `auto-thinking` (same as current) or dedicated Opus via OpenRouter?
   - Option (a): 9Router auto-thinking (cheaper, already configured)
   - Option (b): Dedicated Opus (stronger reasoning, higher cost)

4. **Default profile fate:** Keep default profile running (CLI-only, no gateway) or stop entirely?
   - Recommendation: Keep running CLI-only. It's this current Hermes session.

---

**This checklist is executable. Once Gilang confirms blockers in Section 9, Stage A can begin immediately.**
