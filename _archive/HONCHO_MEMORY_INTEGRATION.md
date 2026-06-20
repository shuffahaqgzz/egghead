# Honcho Memory Integration — Framework v1.1.0

**Date:** 2026-05-16
**Status:** PRODUCTION LIVE
**Cost:** $0/bulan (fully self-hosted, GPU-accelerated)
**Owner:** Hermes engineering profile

---

## Overview

Honcho adalah memory layer untuk Kumachii-Stella framework. Setiap pesan ke 11 agent (default + kumachii + stella + edison + pythagoras + atlas + york + lilith + bonney + stussy + shaka) di-derive jadi observations terstruktur dan disimpan di postgres+pgvector. Agent bisa recall fakta tentang user (Gilang) atau dirinya sendiri lewat `dialectic` query.

**Key promises:**
- Persistent memory across sessions, platforms, profiles
- Single canonical user identity (`gilang`) walau Gilang chat dari CLI / Discord channel mana pun / Telegram
- Per-agent self-representation (kumachii ingat siapa kumachii)
- Cross-agent shared context (semua agent observe ke peer `gilang` yang sama)
- Free, fully on-prem, no external API calls untuk memory operations

---

## Architecture

```
                       ┌─────────────────────────────┐
                       │  10.50.0.111:8500           │
                       │  Honcho API (FastAPI)       │
                       │  + deriver worker × 4       │
                       └──────────┬──────────────────┘
                                  │
              ┌───────────────────┼───────────────────┐
              ▼                   ▼                   ▼
     ┌─────────────────┐  ┌──────────────┐  ┌────────────────┐
     │ honcho-postgres │  │ honcho-redis │  │ honcho-ollama  │
     │ pgvector:pg15   │  │ 8.2-alpine   │  │ ollama:0.6.6   │
     │ docs+peers+sess │  │ cache + queue│  │ qwen2.5:7b     │
     │                 │  │              │  │ + nomic-embed  │
     │  768-dim HNSW   │  │              │  │  GPU resident  │
     └─────────────────┘  └──────────────┘  └────────────────┘
                                                    │
                                                    ▼
                                          ┌─────────────────┐
                                          │  RTX A5000 GPU  │
                                          │  23GB VRAM      │
                                          │  ~7GB used      │
                                          └─────────────────┘

                   ┌─────────────────────────────┐
                   │  Hermes Agent Plugins       │
                   │  plugins/memory/honcho/     │
                   │  (gateway-side integration) │
                   └─────────────────────────────┘
                              │
        ┌─────────────────────┼─────────────────────────┐
        ▼          ▼          ▼          ▼              ▼
   default    kumachii    stella     edison ... (10 profiles)
    gateway    gateway    gateway    gateway       gateway
   systemd    systemd    systemd    systemd       systemd
```

### Stack

| Layer | Implementation | Why |
|---|---|---|
| API server | Honcho v3 (FastAPI) | Official, supports parse() with Pydantic strict json_schema |
| Database | postgres 15 + pgvector | Required by Honcho. HNSW index for ANN search |
| Cache/queue | redis 8.2 | Honcho's queue worker uses Redis for claim coordination |
| LLM inference | Ollama 0.6.6 + qwen2.5:7b-instruct | Free, GPU-accelerated, supports strict json_schema natively (Pydantic-compatible) |
| Embedding | Ollama + nomic-embed-text (768 dim) | Free, fast, matches `documents.embedding` schema |
| GPU | RTX A5000 23GB | Hosts both models simultaneously (qwen2.5:7b 6GB + nomic-embed 849MB) |
| Plugin | `~/.hermes/hermes-agent/plugins/memory/honcho/` | Hermes-side integration, hooks into agent loop |

---

## Configuration Files

### Server config

| File | Purpose |
|---|---|
| `/notflix/apps/honcho/compose.yaml` | Docker compose stack (5 services) |
| `/notflix/apps/honcho/.env` | Per-stage model env vars, postgres password, network binding |
| `/notflix/apps/honcho/source/` | Honcho upstream code (git clone of plastic-labs/honcho) |
| `/notflix/data/honcho/postgres/` | DB volume |
| `/notflix/data/honcho/redis/` | Redis volume |
| `/notflix/data/honcho/ollama/` | Ollama models volume (~5GB persistent) |
| `/notflix/data/honcho/backups/` | Daily pg_dump archives (7-day retention) |

### Hermes-side config

| File | Purpose |
|---|---|
| `~/.hermes/honcho.json` | Global default — `peerName: gilang`, `pinPeerName: true` |
| `~/.hermes/profiles/<agent>/honcho.json` | Per-profile override (`aiPeer`, observation strategy) |
| `~/.hermes/profiles/<agent>/config.yaml` | Has `memory.provider: honcho` |

### Per-stage models (all routed to local Ollama)

```bash
HONCHO_LLM_BASE_URL=http://honcho-ollama:11434/v1
HONCHO_LLM_API_KEY=ollama
HONCHO_DERIVER_MODEL=qwen2.5:7b-instruct
HONCHO_DIALECTIC_MIN_MODEL=qwen2.5:7b-instruct
HONCHO_DIALECTIC_MED_MODEL=qwen2.5:7b-instruct
HONCHO_DIALECTIC_MAX_MODEL=qwen2.5:7b-instruct
HONCHO_SUMMARY_MODEL=qwen2.5:7b-instruct
HONCHO_DREAM_MODEL=qwen2.5:7b-instruct
HONCHO_DERIVER_WORKERS=4
HONCHO_DERIVER_FLUSH_ENABLED=true
```

---

## Peer Identity Model

### Workspace `hermes` peers (12 total)

| Peer | Role |
|---|---|
| `gilang` | Canonical user identity (Gilang). All Gilang's messages from CLI / Discord / Telegram land here via `pinPeerName: true` |
| `hermes` | Default profile's AI peer |
| `kumachii` / `stella` / `edison` / `atlas` / `pythagoras` / `york` / `lilith` / `bonney` / `stussy` / `shaka` | One AI peer per specialist agent |

### `pinPeerName: true` behavior

Without pinning, Honcho plugin forks user identity per platform:
- CLI mode → `gilang` (configured `peerName`)
- Discord direct → Discord user snowflake (e.g. `705848263948894289`)
- Discord-via-bot-relay → Bot's own ID (e.g. `1504034661079322715`)

With `pinPeerName: true` at top-level `honcho.json`, plugin forces all platform identities to the configured `peerName: gilang`. Implemented at `plugins/memory/honcho/session.py:299-307`.

### Observation strategy matrix

Per `~/.hermes/honcho.json` `hosts.<host>.observation`:

| Profile | user.observeMe | user.observeOthers | ai.observeMe | ai.observeOthers | Rationale |
|---|:---:|:---:|:---:|:---:|---|
| default | T | T | T | F | Engineering agent baseline |
| kumachii | T | T | T | **T** | Orchestrator — tracks other agents |
| stella | T | T | T | **T** | Orchestrator — tracks other agents |
| edison | T | T | T | F | Specialist — self only |
| pythagoras | T | T | T | F | Specialist — self only |
| atlas | T | T | T | F | Specialist — self only |
| york | T | T | T | F | Specialist — self only |
| lilith | T | T | T | F | Specialist — self only |
| bonney | T | T | T | F | Specialist — self only |
| stussy | T | T | T | F | Specialist — self only |
| shaka | T | T | T | F | Specialist — self only |

Saving ~25-35% deriver cost vs all-T baseline. Specialists don't need to track each other; orchestrators do.

---

## Recall Patterns

Honcho stores observations in per-`(observer, observed)` collections. Two recall patterns:

### Pattern 1 — Per-agent private view

```bash
POST /v3/workspaces/hermes/peers/<AGENT>/chat
{"target": "gilang", "query": "..."}
```

Returns ONLY observations where `observer=<AGENT>` AND `observed=gilang`. Reflects what THAT agent learned firsthand.

**Use case:** "Stella, apa yang kamu tahu tentang Gilang dari interaksi langsung kalian?"

### Pattern 2 — Globally merged view

```bash
POST /v3/workspaces/hermes/peers/gilang/chat
{"query": "..."}
```

Returns observations where `observed=gilang` ACROSS ALL observers. Honcho dialectic synthesizes from full collection.

**Use case:** "Apa saja fakta tentang Gilang yang ada di sistem (dari kumachii, stella, edison, dll digabung)?"

### Per-observer doc count snapshot (post-migration 2026-05-16)

| Observer | Docs about gilang |
|---|---:|
| gilang (self) | 251 |
| kumachii | 45 |
| stella | 9 |
| hermes | 2 |
| atlas | 1 |
| **Total** | **308** |

---

## Operational Procedures

### Daily backup

```bash
/notflix/apps/honcho/scripts/backup.sh
# pg_dump | gzip → /notflix/data/honcho/backups/honcho_YYYYMMDD_HHMMSS.sql.gz
# 7-day retention, ~3.5MB compressed/day
```

Hermes cron job: `honcho-daily-backup` runs `0 3 * * *`, output `local`.

### Health watchdog

```bash
/notflix/apps/honcho/scripts/health-watchdog.sh
# Silent on healthy. Alerts to Discord on:
#   - Container down
#   - API /health unreachable
#   - qwen2.5:7b unloaded from GPU (auto re-warms)
#   - Queue backlog >100
#   - Disk free <5GB
#   - GPU memory free <2GB
```

Hermes cron job: `honcho-health-watchdog` runs `*/15 * * * *`, output Discord.

### Manual smoke test

```bash
# 1. All containers healthy?
cd /notflix/apps/honcho && docker compose ps

# 2. API responsive?
curl -fsS http://10.50.0.111:8500/health  # → {"status":"ok"}

# 3. Models GPU-resident?
docker exec honcho-ollama ollama ps  # qwen2.5:7b "Forever" + nomic-embed "Forever"

# 4. Pipeline end-to-end?
curl -fsS -X POST http://10.50.0.111:8500/v3/workspaces/hermes/sessions/healthcheck/messages \
  -H "Content-Type: application/json" \
  -d '{"messages":[{"peer_id":"gilang","content":"smoke test message"}]}'
sleep 25
docker logs honcho-deriver 2>&1 | grep "Observation Count" | tail -1
# → expect non-zero count
```

### Recovery cookbook

| Symptom | Cause | Fix |
|---|---|---|
| Watchdog: "qwen2.5:7b unloaded" | OLLAMA_KEEP_ALIVE not applied / Ollama restart | Auto re-warmed by watchdog |
| Queue backlog >100 | Deriver crashed, Ollama down, model slow | `docker compose logs honcho-deriver --tail 50`; restart if dead |
| API 5xx for 5+ min | Postgres/Redis exhaustion or deriver flooding | Check `docker compose ps`; restart unhealthy service |
| GPU memory <2GB | Other service stole VRAM, model leaked | `nvidia-smi`; restart honcho-ollama |
| Disk <5GB | Backup retention broken, log rotation broken | `find /notflix/data/honcho/backups -mtime +7`; check json-file rotation |
| Peer card returns null after 5+ turns | Deriver dead OR work units stuck (Pitfall #20) | Check `SELECT processed, COUNT(*) FROM queue`; verify FLUSH_ENABLED=true |
| User identity fragmented across multiple peers | `pinPeerName` not set | Verify `pinPeerName: true` in honcho.json; restart gateways; run migration SQL (skill: honcho-self-host-via-9router Pitfall #25) |

---

## Verification Test Results (2026-05-16)

### Cross-agent observation derivation

Sent identical Discord message to all 10 agent channels in parallel. Result:

| Agent | Self-observations derived from test | Latency |
|---|---:|---:|
| kumachii | 3 facts | ~25s |
| stella | 1 fact | ~25s |
| edison | 4 facts | ~25s |
| atlas | 3 facts | ~25s |
| pythagoras | 2 facts | ~25s |
| york | 3 facts | ~25s |
| lilith | 2 facts | ~25s |
| bonney | 4 facts | ~25s |
| stussy | 4 facts | ~25s |
| shaka | 6 facts | ~25s |
| **Total** | **32 facts** | — |

All 10 agents loaded Honcho client successfully (verified in `agent.log`: "Memory provider 'honcho' activated" + "Initializing Honcho client" entries).

### Cross-agent privacy isolation

Sent unique fact ("ASUS ProArt PX13 laptop") via kumachii channel only. Then asked all 10 agents (target=gilang).

| Path | Recalls fact? |
|---|:---:|
| `peer=gilang/chat` (global merged) | YES |
| `peer=kumachii/chat target=gilang` | YES |
| `peer=<other-9-agents>/chat target=gilang` | NO (correctly returns "no info") |

Confirms Honcho's per-`(observer, observed)` isolation works AS DESIGNED. To enable cross-agent shared knowledge, route recall through `peer=gilang` (Pattern 2) instead of `target=gilang` (Pattern 1).

### Identity unification migration

Pre-migration: Gilang fragmented across 4 peers (`gilang`, `705848263948894289`, `1504034661079322715`, `hermes`).

Post-migration (single SQL transaction):
- 381 docs migrated (observer side)
- 179 docs migrated (observed side)
- 41 messages updated
- 41 message_embeddings updated
- 15 session_peers updated
- 19 dangling collections cleaned
- 2 proxy peer rows deleted

Result: 308 docs about gilang now under canonical peer. Workspace = 12 peers (was 14).

---

## Known Limitations

1. **qwen2.5:7b reasoning weakness on dialectic recall** — sometimes misses recent observations even when SQL ground truth shows them. Workaround: bump `reasoning_level: medium` or `high` on important recalls.
2. **`<tool_call>` markup leak** — qwen2.5:7b kadang emit tool-call wrapper text di dialectic response. Cosmetic only, content valid. Fix: upgrade to qwen2.5:14b (already pulled, 2x VRAM cost).
3. **Per-(observer,observed) isolation is a feature, not bug** — but it means agents only see facts from conversations they were in. For cross-agent shared knowledge, query `peer=gilang` directly without target.
4. **Idle queue → deriver shows no activity until new message arrives** — normal behavior, not a bug.

---

## Migration Story (For Future Reference)

The path to current production state (chronological):

1. **Initial attempt — 9router upstream** (failed): cx/gpt-5.4 strips `response_format` header for all models → Honcho's `parse()` receives prose, not JSON → 0 observations
2. **OpenCode-Go alt** (failed): only mimo-v2.5-pro passed parse() but returned envelope `{role:..., content:...}` → `explicit=[]`
3. **OpenRouter Gemini/Claude** (worked, paid): ~$0.30-0.80/bulan. Validated Honcho-official tier model assignments
4. **Local Ollama qwen2.5:7b** (current, FREE): 1.4s warm latency, 3/3 success, GPU-resident, equivalent quality
5. **24/7 hardening**: GPU pre-warm, healthchecks, daily backup, Discord watchdog
6. **Identity unification via `pinPeerName: true`**: SQL migration, 308 docs unified to canonical `gilang` peer
7. **Cross-agent recall verified**: per-agent isolation OK, global merge via `peer=gilang` works

---

## Reference Skills

| Skill | Path |
|---|---|
| Deployment & operations | `~/.hermes/skills/multi-agent-framework/honcho-self-host-via-9router/` |
| Backend probing | `scripts/probe_honcho_backend.py` (compatibility check before swapping LLM) |
| 9router debug | `scripts/inspect_9router_keys.py` (Pitfall #24 — auth rotation) |
| Memory config repair | `scripts/repair_memory_config.py` (Pitfall #14 — duplicate keys) |
| Peer pre-create | `scripts/precreate_peers.py` (Pitfall #15, #18) |
| Cross-agent recall test | `scripts/cross_agent_recall_test.py` |
| Health diagnostic | `scripts/check_deriver_health.sh` (Pitfall #20, #21) |
| Verification | `scripts/verify_honcho_observations.sh` |

---

## Status: PRODUCTION LIVE

| Aspect | Value |
|---|---|
| Uptime | 24/7 (systemd-managed, restart=unless-stopped) |
| Cost | $0/bulan |
| Latency (warm) | 1.4s |
| Throughput | 68 work units / 75s burst capacity |
| Backup | Daily 03:00, 7-day retention |
| Monitoring | Watchdog every 15min → Discord alerts |
| Coverage | 11 profiles × all platforms (CLI, Discord, Telegram) |
| Identity | Unified canonical `gilang` peer via `pinPeerName: true` |
| Recall verified | Per-agent isolation + global merge both work |
