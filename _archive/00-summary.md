# Hermes Multi-Agent Architecture — Summary

**Version:** 1.0.0  
**Date:** 2026-05-07  
**Author:** Rosinante / Kumachii  
**Status:** ✅ Production Ready

---

## Overview

Setup hybrid multi-agent architecture dengan 8 specialized agents menggunakan Hermes Agent framework. Arsitektur ini分成 dua phase:

1. **Phase 1 — Light Agents:** 5 agents via `channel_prompts` (1 bot, zero additional RAM overhead)
2. **Phase 2 — Dedicated Agents:** 3 profile agents dengan bot token & model terpisah

---

## Goals

- [x] Deploy 8 specialized agents dengan domain expertise berbeda
- [x] Pisahkan Telegram (default profile only) dari dedicated agents
- [x] Unique Discord bot per dedicated agent dengan dedicated channel
- [x] Dedicated LLM model per agent (cost optimization & specialization)
- [x] Isolated profiles — credential, config, session, memory terpisah
- [x] All gateways running concurrently tanpa token conflict

---

## Quick Status

| Profile | Agent | Model | Provider | Gateway | Status |
|---------|-------|-------|----------|---------|--------|
| default | Kumachii | claude-sonnet-4.5 | EnowXAI | PID 2921994 | ✅ Running |
| coda | CODA (Coding) | claude-opus-4.6 | EnowXAI | PID 808354 | ✅ Running |
| resa | RESA (Research) | glm-5.1 | opencode-go | PID 1152751 | ✅ Running |
| sevi | SEVI (Security) | gpt-5.5 | openai-codex | PID 1210035 | ✅ Running |

**Total RAM:** ~621MB (4 gateways)  
**Total Agents:** 8 (3 dedicated + 5 light)  
**Platforms:** Telegram (default only), Discord (all)

---

## Key Achievements

### 1. Token Conflict Resolution
Bot tokens tidak bisa di-share antar profiles. Solusi: disable Telegram di dedicated profiles + dedicated Discord bot per profile.

### 2. Credential Isolation
Discovery penting: profiles pakai `auth.json` terpisah. Credentials tidak auto-shared. Perlu copy manual dari `~/.hermes/auth.json` ke `~/.hermes/profiles/<name>/auth.json`.

### 3. Model Configuration
Provider switching butuh clear `base_url` dan `api_key` agar pake default dari credential pool.

### 4. Zero Cost Overhead (Light Agents)
5 light agents berjalan di channel_prompts default profile — tidak perlu gateway tambahan.

---

## Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                      srv-test01 (Linux)                         │
│                                                                 │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │                   default profile                        │   │
│  │                   (Kumachii 🤘)                          │   │
│  │                                                          │   │
│  │  Model: claude-sonnet-4.5 @ EnowXAI (10.50.0.111:1430)  │   │
│  │                                                          │   │
│  │  ┌────────────────────────────────────────────────────┐  │   │
│  │  │  GATEWAY: Telegram + Discord                        │  │   │
│  │  │  PID: 2921994 | RAM: ~331MB                         │  │   │
│  │  └────────────────────────────────────────────────────┘  │   │
│  │                                                          │   │
│  │  ┌────────────────────────────────────────────────────┐  │   │
│  │  │  LIGHT AGENTS (channel_prompts)                   │  │   │
│  │  │  Zero additional RAM overhead                     │  │   │
│  │  │                                                     │  │   │
│  │  │  📋 WOOF   → OfficeOps  (ch: 1501446154955657256) │  │   │
│  │  │  ✍️  CONCRET → ContentOps (ch: 1501446213130649640) │  │   │
│  │  │  📚 LICA   → EduLifeOps (ch: 1501446282894512228) │  │   │
│  │  │  💰 FINA   → FinanceOps (ch: 1501446306512502845) │  │   │
│  │  │  📄 DOCA   → DocOps    (ch: 1501446326523527239)  │  │   │
│  │  └────────────────────────────────────────────────────┘  │   │
│  └──────────────────────────────────────────────────────────┘   │
│                                                                 │
│  ┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐  │
│  │   coda profile  │  │   resa profile  │  │   sevi profile  │  │
│  │   (CODA 🤖)     │  │   (RESA 🔬)     │  │   (SEVI 🔒)     │  │
│  │                 │  │                 │  │                 │  │
│  │ Model:          │  │ Model:          │  │ Model:          │  │
│  │ claude-opus-4.6 │  │ glm-5.1         │  │ gpt-5.5         │  │
│  │ @ EnowXAI       │  │ @ opencode-go   │  │ @ openai-codex  │  │
│  │                 │  │                 │  │                 │  │
│  │ Gateway:        │  │ Gateway:        │  │ Gateway:        │  │
│  │ Discord only    │  │ Discord only    │  │ Discord only    │  │
│  │ PID: 808354     │  │ PID: 1152751    │  │ PID: 1210035    │  │
│  │ RAM: ~94MB      │  │ RAM: ~94MB      │  │ RAM: ~94MB      │  │
│  │                 │  │                 │  │                 │  │
│  │ Ch: 1501447...  │  │ Ch: 1501447...  │  │ Ch: 1501447...  │  │
│  └─────────────────┘  └─────────────────┘  └─────────────────┘  │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Next Steps

- [ ] Test all bots di Discord via @mention
- [ ] Customize SOUL.md per profile agent
- [ ] Setup monitoring untuk RAM usage
- [ ] Setup cron jobs untuk scheduled tasks
- [ ] Setup alias untuk easier CLI access

---

## Contact

- **User:** Rosinante
- **Assistant:** Kumachii 🤘
- **Platform:** srv-test01 (infra account)
- **Timezone:** Asia/Jakarta (WIB)
