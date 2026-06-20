# Migration Mapping — Old → New (Framework v1.1.0)

**Date:** 2026-05-13
**Phase:** 5.0 Conceptual Fix — step 3/9
**Purpose:** Pemetaan konkret setiap komponen lama ke target framework v1.1.0. Dokumen ini jadi kontrak migrasi — kalau ada yang tidak ke-cover di tabel di sini, itu gap yang harus diaddress sebelum Phase 5.1 mulai.

---

## 0. Rangkuman

Enam axis migrasi utama:

1. **Runtime** — standalone Python service → Hermes multi-profile
2. **Role** — 2-agent (Kumachii + Stella) → 10-agent roster
3. **Workflow** — 3-tier routing → 7-phase + Gate 0-6
4. **Isolation** — centralized Redis → per-profile memory + STELLA state
5. **Handoff** — JSON schema → markdown/YAML contract
6. **Platform** — Flask HTTP + webhook → systemd + gateway + native Hermes transport

Setiap tabel di bawah cover 1 axis. Kolom `Phase` di setiap tabel menunjukkan di stage mana migrasi concrete dilakukan.

---

## 1. Runtime Migration

### 1.1 Komponen Fisik

| Old (sekarang) | New (target) | Phase | Keputusan |
|---|---|---|---|
| `/home/infra/projects/stella-agent/` (Flask service + webhook) | `~/.hermes/profiles/stella/` (Hermes profile + systemd) | 5.1-5.2 | Deprecate Flask, orchestration logic pindah ke STELLA profile SOUL.md + skills |
| `/home/infra/projects/kumachii-executor/` (Python executor) | `~/.hermes/profiles/edison/` (Hermes profile) + reference library tetap | 5.2-5.4 | Rename konseptual jadi EDISON. Directory filesystem tidak segera di-rename — simbolik link + README reframe. Actual rename saat Stage B (5.4). |
| `systemctl start stella-webhook.service` | `systemctl --user start hermes-gateway-stella.service` | 5.1 | Gateway template dari framework Section 13 |
| (tidak ada KUMACHII runtime asli) | `~/.hermes/profiles/kumachii/` (new from scratch) | 5.1 | Framework Section 7.1 |
| (tidak ada 7 agent lain) | 7 profile dedicated (shaka, pythagoras, atlas, york, lilith, bonney, stussy) | 5.4-5.5 | Stage B-E rollout |

### 1.2 Systemd Service

| Old | New |
|---|---|
| (manual Python process via nohup/tmux) | `hermes-gateway-kumachii.service` (user systemd) |
| `stella-webhook.service` (kalau pernah ada) | `hermes-gateway-stella.service` |
| (n/a) | 8 service lainnya (shaka, edison, pythagoras, atlas, york, lilith, bonney, stussy) |

**Template:** Framework Section 13 di file `kumachii-stella-multi-agent-workflow-framework-v1.1.0-final.md`.

### 1.3 Runtime Transport

| Use case | Old | New |
|---|---|---|
| User → agent (Telegram) | Flask `/webhook` endpoint → Stella parse | Hermes gateway Telegram adapter → KUMACHII profile |
| Agent → agent | HTTP POST + JSON body | Handoff markdown file (docs/intake/..., docs/epics/...) + optional direct chat via Discord thread |
| Agent internal tool call | Python functions in-process | Hermes tool system (terminal, file, web, delegation, kanban, dll.) |
| Credential access | `os.environ` + `.env` per-service | `~/.hermes/profiles/<agent>/auth.json` + `.env` per-profile |

---

## 2. Role Migration

### 2.1 Agent Identity Correction

| Old code/directory | Old label | Framework v1.1.0 real role | Action |
|---|---|---|---|
| `stella-agent/` | Stella = orchestrator + executor hybrid | STELLA = orchestrator only (Section 7.2) | Hapus executor responsibility, strict orchestrator |
| `kumachii-executor/` | Kumachii = executor (invest → plan → execute → verify) | Ini role **EDISON** (Section 7.4) | Rename konseptual, file code jadi EDISON reference library |
| (n/a) | - | KUMACHII sebenarnya = personal assistant + intake (Section 7.1) | Build from scratch, beda total dari code existing |

### 2.2 Role Responsibility Split

| Function lama | Lama dikerjakan oleh | Baru dikerjakan oleh | Catatan |
|---|---|---|---|
| User intake (NL parse) | Stella | KUMACHII | Section 7.1 |
| Task classification (execute vs decision vs approval) | Stella | KUMACHII (intake triage) + STELLA (delegation) | KUMACHII triage initial, STELLA keep control |
| Delegation ke executor | Stella | STELLA (phase-based) | Section 7.2 |
| Investigate + Plan + Execute + Verify | Kumachii | EDISON (untuk code) / PYTHAGORAS (untuk research) | Section 7.4 + 7.5 |
| Architecture decisions | (implicit stella) | SHAKA | Section 7.3 — new agent |
| Security review | (none explicit) | ATLAS | Section 7.6 — new agent, can block |
| QA review | Kumachii verify phase | YORK | Section 7.7 — new agent, can block |
| Deployment | Kumachii executor | LILITH | Section 7.8 |
| Documentation | (ad-hoc by stella) | BONNEY | Section 7.9 — new agent, owns RAG |
| Monitoring | (none explicit) | STUSSY | Section 7.10 — new agent, can block |
| Research | Kumachii investigate | PYTHAGORAS | Section 7.5 — read-only |

---

## 3. Workflow Migration

### 3.1 Axis Shift — Tier → Phase

| Old axis (3-tier) | New primary axis (7-phase) | New secondary axis (metadata) |
|---|---|---|
| Tier 1: Low-risk auto-execute | Phase 0-6 determined by lifecycle stage | `risk_level: low` di handoff |
| Tier 2: Medium-risk approval gate | — | `risk_level: medium` di handoff |
| Tier 3: High-risk mandatory approval | Gate 5 (Deploy) dengan human approval; Gate 2/4 juga butuh approval strategic | `risk_level: high` di handoff |

**Implementasi:** Phase + gate = primary control. Tier jadi field metadata di handoff untuk enrichment (approval SLA calculation, logging). Phase menentukan **siapa owner** dan **apa activity**-nya, tier menentukan **seberapa hati-hati**.

### 3.2 Phase Ownership

| Phase | Owner | Lama | Baru |
|---|---|---|---|
| 0 Discovery | KUMACHII / STELLA | Stella parse + route | KUMACHII intake simple / STELLA intake complex |
| 1 Planning | STELLA | Kumachii plan | STELLA create PRD + stories |
| 2 Solutioning | SHAKA | (not explicit) | SHAKA architecture + ADRs |
| 3 Implementation | EDISON | Kumachii execute | EDISON TDD per-story |
| 4 Review | YORK | Kumachii verify | YORK review-only + ATLAS security |
| 5 Deploy | LILITH | (DEPLOYMENT_GATE.md spec only) | LILITH gated deploy |
| 6 Operate | STUSSY | (not explicit) | STUSSY monitoring + runbook |

### 3.3 Gate Enforcement

| Old approval | New gate |
|---|---|
| Production deploy approval | Gate 5 "Ready to Deploy" |
| (No explicit architecture approval) | Gate 2 "Architecture Approved" |
| (No explicit review pass) | Gate 4 "Review Passed" |
| (No explicit discovery gate) | Gate 0 "Discovery Approved" |
| (No explicit PRD gate) | Gate 1 "PRD Approved" |
| (No explicit impl gate) | Gate 3 "Implementation Complete" |
| (No explicit operate gate) | Gate 6 "Operate Ready" |

**Semua gate wajib**. STELLA adalah gate enforcer. User (Gilang) = final approver di gate berisiko (terutama 2, 4, 5).

---

## 4. Memory / Isolation Migration

### 4.1 Storage Backend

| Data kind | Old location | New location | Owner | Phase |
|---|---|---|---|---|
| Task state | `Redis task:{id}:*` (global) | `Redis task:{id}:*` (STELLA-scoped namespace) | STELLA | 5.2 |
| Tool call logs | `Redis task:{id}:execution.*` | Hermes native session (per-profile) | Each profile | 5.1 |
| Intermediate vars | `Redis task:{id}:intermediate.*` | Hermes native session (per-profile) | Each profile | 5.1 |
| Approval state | `Redis task:{id}:approval.*` | `Redis task:{id}:approval.*` (STELLA-scoped) + handoff markdown | STELLA | 5.3 |
| Temp files | `/tmp/task_{id}_*` | `/tmp/hermes-<profile>-<session>/` | Each profile | 5.1 |
| User preferences | `~/.hermes/knowledge/global/USER_PROFILE.md` | `~/.hermes/profiles/kumachii/memory/user.md` + shared KB cross-ref | KUMACHII + shared KB | 5.1 |
| Project tech stack | `~/.hermes/knowledge/projects/<project>/TECH_STACK.md` | Same (shared KB) | BONNEY curate | 5.5 |
| Error patterns | `~/.hermes/knowledge/projects/<project>/ERROR_PATTERNS.md` | Same (shared KB) | BONNEY curate | 5.5 |
| Decision log | `~/.hermes/knowledge/projects/<project>/DECISION_LOG.md` | Same (shared KB) | STELLA write, BONNEY curate | 5.1 |
| Architecture decisions | (mixed with DECISION_LOG) | `~/.hermes/profiles/shaka/memory/` (private) + `~/.hermes/knowledge/.../ADRs/` (shared) | SHAKA | 5.5 |
| Security policies | (none explicit) | `~/.hermes/profiles/atlas/memory/` (private) | ATLAS | 5.5 |
| QA checklists | (none explicit) | `~/.hermes/profiles/york/memory/` (private) | YORK | 5.5 |
| Deploy references | (none explicit) | `~/.hermes/profiles/lilith/memory/` (private) | LILITH | 5.5 |
| Docs standards + RAG index | (none) | `~/.hermes/profiles/bonney/memory/` (private) + vector DB / BM25 index (Phase 5.3 decision) | BONNEY | 5.5 |
| Operational baselines | (none explicit) | `~/.hermes/profiles/stussy/memory/` (private) | STUSSY | 5.5 |
| Research notes | (mixed with KB) | `~/.hermes/profiles/pythagoras/memory/` (private) + citable docs in shared KB | PYTHAGORAS | 5.4 |
| Implementation patterns | (kumachii session) | `~/.hermes/profiles/edison/memory/` (private) | EDISON | 5.4 |

### 4.2 Memory Rules Shift

| Old rule | New rule (Section 18.2) |
|---|---|
| Session → persistent sync automatic di end-of-task | Per-profile memory write is automatic. Shared KB write via BONNEY ingest pipeline (Phase 5.5). |
| `mark_for_persist` API | Handoff markdown = default sharing mechanism. BONNEY ingest pipeline formalizes shared KB updates. |
| Single access namespace | Sensitive memory not auto-shared. Cross-agent read goes via handoff or explicit shared KB doc. |
| Session 24h TTL | Hermes native session store handles lifecycle per-profile |

### 4.3 Redis Role

| Lama | Baru |
|---|---|
| Global session cache + task state + cross-agent coordination | **STELLA workflow state only** (task_id, phase, gate status, approval state) |
| Port 6379 shared | Bisa tetap port 6379 tapi keyspace scoped: `stella:task:*`, `stella:gate:*`, `stella:approval:*` |
| Semua agent read/write | Hanya STELLA profile yang connect ke Redis. Agent lain tidak tahu Redis ada. |

---

## 5. Handoff Migration

### 5.1 Format Shift

| Aspect | Old (JSON) | New (Markdown) |
|---|---|---|
| Transport | HTTP POST body / Redis publish | File di `docs/intake/`, `docs/epics/`, `docs/reviews/` (git-committable) + chat message rendering |
| Human-readable | Perlu tool / pretty-print | Native |
| Git-trackable | Bisa tapi tidak natural | Native |
| Telegram/Discord rendering | No | Ya (markdown auto-render) |
| Validation | JSON schema | Template compliance check (ada semua field mandatory?) |
| Custom parser | Perlu | Tidak (markdown universal) |

### 5.2 Schema Mapping

Field lama JSON → field markdown:

| Old JSON field | New markdown field |
|---|---|
| `task_id` | `Task ID:` |
| `type` | `Phase:` (baru, ganti concept) |
| `goal` | `Objective:` |
| `context.project` | `Inputs:` (project reference) |
| `context.environment` | `Constraints:` (environment constraint) |
| `context.branch` | `Inputs:` (branch reference) |
| `constraints[]` | `Constraints:` |
| `success_criteria[]` | `Acceptance criteria:` |
| `metadata.priority` | `Priority:` |
| `metadata.sla_seconds` | `Constraints:` (SLA line) |
| `metadata.source` | `From:` (KUMACHII → STELLA) |
| `decisions_made[]` | `Decisions made:` |
| `status` | `Status:` (PASS / CONCERNS / BLOCKED) |
| `errors[]` | `Open risks:` |
| `next_steps[]` | `Next action:` |

Template lengkap sudah ada di framework Section 10. Template YAML variant juga tersedia kalau perlu machine-parseable.

### 5.3 Handoff Files Location

| Handoff type | Old | New |
|---|---|---|
| User → KUMACHII intake | (parse di Stella inline) | `docs/intake/task-brief-<id>.md` |
| KUMACHII → STELLA | (Stella HTTP call) | `docs/intake/handoff-<id>.md` (KUMACHII menulis) |
| STELLA → specialist | JSON HTTP POST | `docs/epics/epic-<id>.md` (STELLA menulis, dipindah ke assigned agent chat/profile) |
| Specialist → STELLA (result) | JSON result | `docs/reviews/review-<id>.md` (specialist menulis, STELLA menerima) |
| Gate approval | (Telegram message) | `docs/gates/gate-<N>-<id>.md` (STELLA generate, Gilang approve) |
| User decision | (Telegram free-text) | `docs/approvals/approval-<id>.md` (STELLA capture dari chat, commit to git) |

---

## 6. Platform Migration

### 6.1 Telegram Bot

| Old | New |
|---|---|
| Bot token dipakai Stella webhook service | Bot token exclusive untuk KUMACHII (Section 12.2) |
| Webhook di `https://<domain>/webhook` (Flask) | Hermes gateway Telegram adapter (native) |
| Manual parsing message | Hermes session + tool system |

### 6.2 Discord Bot (baru)

| Old | New |
|---|---|
| (tidak dipakai untuk agent) | Tiap specialist agent (STELLA, SHAKA, EDISON, ..., STUSSY) pakai Discord channel/thread dedicated |
| (n/a) | **9 bot token unique**, 1 per profile. Tidak boleh share token (Section 12.1). |

### 6.3 Environment Variables

| Old `.env` (per project) | New `.env` (per profile) |
|---|---|
| `/home/infra/projects/stella-agent/.env` | `~/.hermes/profiles/stella/.env` |
| `/home/infra/projects/kumachii-executor/.env` | `~/.hermes/profiles/edison/.env` + reference library env tetap |
| `~/.hermes/.env` (shared) | Tetap untuk cross-profile shared secrets, tapi per-profile override priority (Section 11.3) |

Rule Section 12.3: jangan set unused token variable ke empty string — comment out.

---

## 7. Tool & Skill Migration

### 7.1 Hermes Skills

| Old Python function (in `kumachii-executor`) | New Hermes skill / reference |
|---|---|
| `investigate(task)` Python function | Skill di EDISON profile: `edison-investigate.md` (or inline di SOUL.md) |
| `plan(task)` Python function | Skill: `edison-plan.md` |
| `execute(plan)` Python function | Standard Hermes tools (terminal, file, web) + EDISON's TDD workflow |
| `verify(task)` Python function | Skill: `edison-verify.md` + standard test runner |
| `memory_layer.py` (sync ke KB) | BONNEY RAG ingest pipeline (Phase 5.5) |
| `monitoring.py` (health snapshot) | STUSSY profile + `stussy-healthcheck.md` skill (Phase 5.5) |

### 7.2 Reference Library Plan

`/home/infra/projects/kumachii-executor/src/` akan jadi **reference library**, dipanggil via skill EDISON. Tidak di-rename direktorinya langsung di Phase 5.0 — hanya ditambah:
- `README.md` reframe note yang declare "role = EDISON reference impl"
- Wrapper skill di `~/.hermes/skills/` atau `~/.hermes/profiles/edison/skills/` yang invoke Python functions-nya

Actual rename direktori → `edison-executor/` di Phase 5.4 saat EDISON profile live.

---

## 8. Hal Yang **Tidak** Dimigrasi (Preserved As-Is)

Ini penting disebut jelas supaya tidak invested waktu migrasi komponen yang sudah OK:

| Komponen | Alasan preserve |
|---|---|
| `~/.hermes/knowledge/global/USER_PROFILE.md` | Gilang preferences — struktur OK, pindah owner ke KUMACHII + BONNEY curate. |
| `~/.hermes/knowledge/global/INFRASTRUCTURE.md` | Server config — tetap valuable, BONNEY curate. |
| `~/.hermes/knowledge/projects/<project>/TECH_STACK.md` | Pattern per-project tetap sesuai framework Section 20. |
| `~/.hermes/knowledge/projects/<project>/ERROR_PATTERNS.md` | Sesuai BONNEY Section 7.9 maintain runbook + ERROR_PATTERNS. |
| `~/.hermes/knowledge/projects/<project>/DECISION_LOG.md` | Cocok jadi `docs/ADRs/` alternative per framework Section 20. |
| Git versioning pattern | Sesuai framework Section 11 (audit trail). |
| Terse communication style | Sejalan Section 3.7 + Layer 5 token optimization. |
| Task ID pattern `task_YYYYMMDD_NNN` | Compatible dengan framework handoff. |

---

## 9. Migration Risks & Mitigations

| Risk | Probability | Impact | Mitigation |
|---|---|---|---|
| Stella workflow state Redis key collision antara lama & baru | Medium | Data corruption | Namespace scope `stella:*` prefix, dan flush old Redis keys saat stage 5.2 |
| Gilang message ke old Telegram bot tidak sampai | Medium | UX broken | Stop Flask webhook saat Hermes gateway-kumachii nyala, transition window minimize |
| Credentials copy terlewat antar-profile | High | Agent down karena missing API key | Checklist isolation test (Section 24.3) di tiap stage rollout |
| `kumachii-executor/` code di-import dari tempat lain di server | Low | Build break kalau di-rename | Grep dulu semua import, keep path lama di Stage 5.4 sampai confirm no external ref |
| Markdown handoff miss field mandatory | Medium | Agent bingung / task stuck | Template validator skill (parse markdown → cek required field) |

---

## 10. Checklist Pre-Flight untuk Stage 5.1

Sebelum start Stage 5.1 (foundation), confirm:

- [ ] File `PHASE_5_0_CONCEPTUAL_FIX_PLAN.md` dibaca & disetujui Gilang
- [ ] File `SPEC_AUDIT.md` dibaca
- [ ] File ini (`MIGRATION_MAPPING.md`) dibaca
- [ ] Backup `~/.hermes/specs/` → `~/.hermes/specs-legacy-2026-05/` (manual rename dilakukan Gilang atau dengan approval)
- [ ] Backup `/home/infra/projects/stella-agent/` (git tag `pre-framework-v1.1.0-migration`)
- [ ] Backup `/home/infra/projects/kumachii-executor/` (git tag `pre-framework-v1.1.0-migration`)
- [ ] Telegram bot token di-identify — akan di-claim exclusive KUMACHII
- [ ] Discord server/workspace ready untuk host bot specialist (boleh disiapkan nanti, pre-req di 5.2 bukan 5.1)
- [ ] Model strategy clear per agent (Section 14) — at minimum KUMACHII + STELLA decided
- [ ] `~/.hermes/profiles/` directory empty (fresh start)

---

**Next step Phase 5.0:** Reframe note di `/home/infra/projects/kumachii-executor/` + `/home/infra/projects/stella-agent/` (non-destructive, cuma README notes).
