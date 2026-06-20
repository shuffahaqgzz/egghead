# Phase 5.0 — Conceptual Fix Plan (KUMACHII-STELLA Framework v1.1.0)

**Version:** 1.0.0
**Date:** 2026-05-13
**Author:** Hermes (Opus 4.7 via 9Router)
**Status:** Active — decision locked by Gilang
**Chosen path:** Opsi A — Full Framework Compliance
**Purpose:** Master plan yang mengunci arah arsitektur baru sebagai *new lifecycle development* untuk project ini. Semua plan lain (old Phase 5 single-profile wrap) dinyatakan **mati** dengan dokumen ini.

---

## 0. Decision Lock

| Topik | Keputusan | Sumber |
|---|---|---|
| Strategi migrasi | **Opsi A — Full Framework Compliance** | Gilang, 2026-05-13 |
| Model lifecycle | 6-layer framework + 7-phase + Gate 0-6 | Framework v1.1.0 Section 2 + 8 + 19 |
| Runtime | Hermes multi-profile (10 dedicated) | Framework v1.1.0 Section 11 |
| Agent roster | 10 agents (KUMACHII…STUSSY) | Framework v1.1.0 Section 5 |
| Old Phase 5 plan | **CANCELLED** (single-profile wrap) | Framework compliance review |
| Existing standalone code | Reference library, not runtime | Section 4.1 review doc |

**Rule setelah dokumen ini ditulis:** plan implementasi apa pun **harus** trace balik ke 1 dari 6 layer, 1 dari 10 role, 1 dari 7 phase, atau 1 dari 6 gate. Kalau tidak bisa — ditolak.

---

## 1. Kenapa Ini Ditulis

Sebelumnya Phase 1-4 selesai sebagai standalone Python MVP (Stella Flask service + Kumachii executor + Redis + KB). Review compliance (2026-05-12) menemukan ~25% aligned dengan framework v1.1.0 — arah arsitektur divergen di runtime, role, workflow, isolation, dan handoff.

Gilang pilih Opsi A = rewrite ke Hermes-native multi-profile, full framework.

Dokumen ini = **kontrak eksekusi** untuk migrasi itu. Bukan saran lagi — ini plan of record.

---

## 2. The Six-Layer Foundation (Non-Negotiable)

Framework v1.1.0 Section 2 mandate: arsitektur ini dibangun di atas **6 layer**. Semua implementasi harus honor semuanya — drop 1 layer = framework broken.

### Layer 1 — SOP Discipline
- Alignment before execution (intent, scope, success criteria jelas sebelum kerja)
- Smart Zone (focused context, vertical slicing)
- TDD (RED → GREEN → REFACTOR → VERIFY → REVIEW)
- Human-in-the-loop untuk risky decisions
- Documents as contracts, bukan dekorasi

**Implementasi check:**
- [ ] Setiap task baru ada task-brief.md sebelum eksekusi
- [ ] TDD enforced di EDISON profile (Gate 3 block kalau tests tidak ada)
- [ ] Human approval hook untuk Gate 5 (deploy) mandatory

### Layer 2 — BMAD Structure
- **B**usiness discovery → **M**odel planning → **A**rchitecture solutioning → **D**elivery (impl + review + deploy + operate)
- Mapped ke 7-phase lifecycle (Phase 0-6)

**Implementasi check:**
- [ ] Setiap project punya `docs/` structure lengkap (product-brief, PRD, architecture, ADRs, runbook)
- [ ] Phase transitions dikontrol gate, bukan ad-hoc

### Layer 3 — OMO-Style Orchestration
- **O**rchestrator = STELLA, single owner workflow state
- **M**any specialists = 8 dedicated agents
- **O**perator = User (Gilang), final approver

Read-only consultation: PYTHAGORAS, BONNEY bisa konsultasi cross-agent tanpa modify state.

**Implementasi check:**
- [ ] STELLA satu-satunya yang pegang workflow state (Redis/state store scoped ke STELLA profile)
- [ ] Specialist agent tidak delegate ke specialist lain langsung — semua routed via STELLA
- [ ] PYTHAGORAS + BONNEY read-only access ke shared KB

### Layer 4 — Hermes Runtime Isolation
- 1 profile = 1 agent = 1 systemd service
- Isolated credentials, memory, sessions, skills
- No shared bot tokens

**Implementasi check:**
- [ ] `~/.hermes/profiles/<agent>/` ada 10 direktori lengkap
- [ ] `auth.json` per profile tidak overlap credential
- [ ] `hermes-gateway-<agent>.service` systemd unit per profile

### Layer 5 — Token Optimization
- RTK untuk compress command output (git, docker, kubectl)
- Caveman untuk repeated status reports
- code-review-graph untuk codebase besar
- LSP + AST-grep untuk deterministic code nav
- RAG retrieval instead of dumping docs
- Context reset antar phase

**Implementasi check:**
- [ ] BONNEY RAG pipeline serving untuk PYTHAGORAS + EDISON + SHAKA queries
- [ ] Context reset enforced di phase transition (new session per phase untuk complex project)

### Layer 6 — Operational Control
- Monitoring (STUSSY)
- Runbooks (LILITH + STUSSY maintain, BONNEY catalog)
- Rollback path mandatory sebelum deploy
- Incident response flow
- Periodic review cadence

**Implementasi check:**
- [ ] STUSSY profile active sebelum production deploy apa pun
- [ ] Setiap Gate 5 butuh rollback doc
- [ ] Weekly health check agenda di STUSSY runbook

---

## 3. Agent Roster (10) — Role Lock

Role binding. Dokumen ini mengunci definisi role — tidak ada drift lagi.

| # | Agent | Role | Stage | Platform | Memory Scope |
|---|---|---|---|---|---|
| 1 | **KUMACHII** | Personal Assistant + Intake | A | Telegram (primary) + Discord (optional) | Personal preferences |
| 2 | **STELLA** | Orchestrator | A | Discord | Workflow state |
| 3 | **EDISON** | Coding (TDD) | B | Discord | Implementation patterns |
| 4 | **PYTHAGORAS** | Research (read-only) | B | Discord | Research notes |
| 5 | **ATLAS** | Security Review (can block) | C | Discord | Security policies |
| 6 | **YORK** | QA/Reviewer (can block) | C | Discord | QA checklists |
| 7 | **LILITH** | DevOps (gated) | D | Discord | Deployment refs |
| 8 | **BONNEY** | Documentation + RAG | D | Discord | Docs standards + RAG index |
| 9 | **STUSSY** | Monitoring + Ops (can block) | D | Discord | Operational baselines |
| 10 | **SHAKA** | Architect (ADRs) | E | Discord | Architecture decisions |

**Stage rollout order:** A → B → C → D → E. **Tidak boleh loncat.**

Catatan role correction dari keadaan sekarang:
- Yang ada di `/home/infra/projects/kumachii-executor/` = role **EDISON** (Coding TDD), bukan KUMACHII.
- KUMACHII yang benar belum pernah dibangun — dia adalah personal assistant user-facing, bukan executor.

---

## 4. Workflow Lifecycle — 7 Phase + 6 Gate

Alur kerja utama project ini. Framework v1.1.0 Section 8.

```
Phase 0: Discovery    (Owner: KUMACHII/STELLA)  → Gate 0: Discovery Approved
Phase 1: Planning     (Owner: STELLA)           → Gate 1: PRD Approved
Phase 2: Solutioning  (Owner: SHAKA)            → Gate 2: Architecture Approved
Phase 3: Implementation (Owner: EDISON)         → Gate 3: Implementation Complete
Phase 4: Review       (Owner: YORK)             → Gate 4: Review Passed
Phase 5: Deploy       (Owner: LILITH)           → Gate 5: Ready to Deploy
Phase 6: Operate      (Owner: STUSSY)           → Gate 6: Operate Ready
```

**Gate rule:**
- PASS → phase selanjutnya
- CONCERNS → lanjut tapi monitor
- FAIL → stuck, harus remediate

**STELLA adalah gate enforcer.** Gilang adalah final approver untuk gate PASS di phase-phase berisiko (Gate 2, 4, 5).

Template gate ada di framework v1.1.0 Section 19 — akan di-copy jadi `docs/gates/gate-<N>-template.md` di setiap project.

**Penting:** ini **bukan** replace dari 3-tier routing lama. Dua axis beda:
- Phase = **workflow discipline** (tahap mana di lifecycle)
- Tier = **risk classification** (seberapa berbahaya action)

Phase-based adalah axis utama. Tier-based bisa survive sebagai metadata ekstra di handoff (risk tier field), tapi tidak jadi primary routing lagi.

---

## 5. RAG Architecture (BONNEY)

Framework v1.1.0 Section 16. RAG adalah concern utama, bukan optional.

### 5.1 Source Hierarchy (Authority Ladder)

1. Source-of-truth documents
2. Architecture + ADRs
3. PRD + acceptance criteria
4. Code + tests
5. Deployment + runbook docs
6. Research reports
7. Generated summaries
8. Chat history

Generated summary **tidak pernah** override primary source. BONNEY enforce ini saat ingest.

### 5.2 Pipeline

```
Ingest → Clean → Chunk → Tag → Embed → Index → Retrieve → Rerank → Answer → Cite → Validate
```

### 5.3 Chunk Metadata (Mandatory)

```yaml
source_file:
source_type:        # prd | architecture | adr | code | runbook | research | summary | chat
owner_agent:        # which agent owns this doc
created_at:
updated_at:
project:
phase:              # 0-6
version:
freshness:          # fresh | stale | deprecated
authority_level:    # 1-8 sesuai hierarchy
contains_secret: false
```

### 5.4 Rules (Non-Negotiable)

- Never ingest secrets / raw credentials
- Mark stale content explicitly
- Cite source pada setiap retrieval
- Retrieval test wajib untuk docs penting
- Re-index setelah major doc change
- Source-of-truth preserved

### 5.5 Storage Decision (TBD di Phase 5.3)

Kandidat yang akan dievaluasi:
- **Option RAG-1:** Local vector DB (chroma, qdrant, lancedb) — simple, self-host
- **Option RAG-2:** File-based + BM25 (tantivy, ripgrep) — no vector, fast, cheap
- **Option RAG-3:** Hybrid (BM25 + embedding re-rank) — best quality, more infra

Keputusan: defer ke Phase 5.3, setelah Stage A stabil. Decision owner: BONNEY + PYTHAGORAS research.

---

## 6. Adaptability Verification — Framework Bisa Dilanjutkan

Cek setiap layer: apakah implementasi Stage A bisa berkembang ke 10-agent tanpa rewrite.

| Layer | Stage A (2 agent) | Path ke 10 agent | Rewrite? |
|---|---|---|---|
| 1. SOP Discipline | Alignment docs + TDD hooks di EDISON dummy | Pattern sama, tinggal diterapkan di tiap agent baru | **No** |
| 2. BMAD Structure | Phase 0-1 aktif (KUMACHII intake → STELLA plan) | Tambah phase 2-6 saat owner agent masuk | **No** |
| 3. OMO Orchestration | STELLA sebagai hub, handoff ke KUMACHII only | Tambah fan-out ke SHAKA, EDISON, dst. hub tetap | **No** |
| 4. Hermes Isolation | 2 profile + 2 systemd service | Copy template, tambah 8 profile baru | **No** |
| 5. Token Optimization | Context reset + simple summary | BONNEY activate di Stage D, retrofitted tanpa rewrite orchestration | **No** |
| 6. Operational Control | Minimal logging di STELLA | STUSSY activate di Stage D, pasang monitor tanpa ubah Stella | **No** |

**Verdict:** Semua 6 layer additive-friendly. Stage A foundation = scalable base, bukan throwaway. Kalau ada drift arah yang bikin layer lama kudu rewrite, itu sinyal keputusan salah.

**Adaptability test metric untuk Stage A exit:**
- [ ] Tambah profile ke-3 (contoh: EDISON dummy) tanpa ubah KUMACHII atau STELLA config
- [ ] Tambah handoff markdown baru tanpa ubah parser
- [ ] Tambah gate phase baru tanpa ubah STELLA core logic

---

## 7. Revised Stage Roadmap

### Stage 5.0 — Conceptual Fix (INI) — 1 hari
- Tulis plan ini (done dengan commit file ini)
- Audit 7 spec di `~/.hermes/specs/`
- Buat migration mapping old → new
- Reframe nama (kumachii-executor konsep = edison, bukan kumachii)
- Draft AGENTS.md + RAG plan + Stage A checklist

**Exit:** dokumen plan 5.0 paket lengkap tersimpan di `~/.hermes/knowledge/topics/framework-v1.1.0/`.

### Stage 5.1 — Hermes Foundation (Stage A) — 2-3 hari
- `hermes profile create kumachii` + config Telegram + model balanced
- `hermes profile create stella` + config Discord + model strongest
- Systemd services `hermes-gateway-kumachii.service` + `hermes-gateway-stella.service`
- SOUL.md per profile (dari Section 7.1 + 7.2 framework)
- Test isolation (chat test, credential isolation test, token conflict test)
- Test handoff markdown KUMACHII → STELLA

**Exit:** Section 24 framework test items PASS untuk 2 profile.

### Stage 5.2 — Migrate Existing Logic — 3-5 hari
- `kumachii-executor` Python module → reference library yang dipanggil EDISON skill (**tapi EDISON profile belum dibuat di Stage 5.2, hanya simpan library**)
- `stella-agent` Flask service → deprecate, logic orchestration pindah ke STELLA profile SOUL.md + skills
- Redis role revised: STELLA state store only
- Memory layer migrasi ke per-profile `~/.hermes/profiles/<agent>/memory/`

**Exit:** tidak ada lagi standalone Flask service running. STELLA profile 100% owner workflow state.

### Stage 5.3 — Handoff + Gates Infrastructure — 2-3 hari
- Markdown handoff template generator (bisa Hermes skill)
- Gate 0-6 template files + STELLA skill untuk generate gate doc
- End-to-end test KUMACHII → STELLA handoff + Gate 0 dokumen ter-generate

**Exit:** handoff protocol markdown working end-to-end.

### Stage 5.4 — Build Layer (Stage B) — 1 minggu
- `hermes profile create edison` + TDD skills + reference ke library dari 5.2
- `hermes profile create pythagoras` + research skill + read-only KB access
- End-to-end test: STELLA delegate research ke PYTHAGORAS + coding ke EDISON

**Exit:** Stage B exit criteria framework terpenuhi.

### Stage 5.5 — Control + Operate + Architect (Stage C, D, E) — 2-3 minggu
- Stage C: ATLAS + YORK
- Stage D: LILITH + BONNEY + STUSSY (BONNEY bawa RAG online)
- Stage E: SHAKA

**Exit:** full 10-agent deployment, Gate 6 Operate Ready untuk framework itu sendiri.

**Total estimasi:** ~5 minggu untuk full compliance, mengikuti review doc.

---

## 8. Definition of Done — Phase 5.0

Phase 5.0 selesai kalau:

- [ ] `PHASE_5_0_CONCEPTUAL_FIX_PLAN.md` tersimpan (dokumen ini)
- [ ] `SPEC_AUDIT.md` audit 7 file `~/.hermes/specs/`
- [ ] `MIGRATION_MAPPING.md` old→new full table
- [ ] Reframe note untuk `kumachii-executor/` + `stella-agent/`
- [ ] `AGENTS.md` master + 10 SOUL.md draft per agent
- [ ] `RAG_ARCHITECTURE_PLAN.md` untuk BONNEY
- [ ] `STAGE_A_FOUNDATION_CHECKLIST.md` Stage A executable checklist
- [ ] Adaptability verification hasil check (di plan ini, Section 6 ✓)
- [ ] Final report ke Gilang

---

## 9. Risks & Mitigations

| Risk | Impact | Mitigation |
|---|---|---|
| Stuck di Stage A terlalu lama (isolation config susah) | Delay seluruh roadmap | Time-box 3 hari, kalau stuck escalate untuk simplifikasi (misalnya lewatin systemd, pakai tmux dulu) |
| Existing code di kumachii-executor jadi dead weight | Waste investment Phase 1-4 | Reframe jadi reference library, dipanggil via skill di EDISON (Stage 5.2) |
| Multi-profile auth berat (10 credential pool) | Maintenance overhead | Credential pool reuse di level provider (satu OpenRouter key bisa share, tapi bot token unik per agent) |
| RAG infra over-engineered | Bottleneck di Stage D | Mulai file-based BM25 dulu, upgrade ke vector DB kalau butuh |
| Framework terlalu strict untuk task kecil | Over-engineering untuk personal flow | KUMACHII boleh bypass STELLA untuk personal task (Section 3.1 rule) |

---

## 10. Document Ownership

| Dokumen | Owner | Update trigger |
|---|---|---|
| `PHASE_5_0_CONCEPTUAL_FIX_PLAN.md` (ini) | Hermes + Gilang | Decision change strategic |
| `SPEC_AUDIT.md` | Hermes | Setelah audit spec done |
| `MIGRATION_MAPPING.md` | Hermes + Gilang | Tiap kali komponen dimigrasi |
| `AGENTS.md` | STELLA (setelah online) | Role drift atau rule change |
| `RAG_ARCHITECTURE_PLAN.md` | BONNEY (setelah online) | Pipeline change |
| Per-agent SOUL.md | Profile owner (self) | Role refinement |
| Gate templates | STELLA | Gate checklist revisi |

---

## 11. Referensi

- Framework source: `~/.hermes/knowledge/topics/kumachii-stella-multi-agent-workflow-framework-v1.1.0-final.md`
- Compliance review: `~/.hermes/knowledge/topics/kumachii-stella-framework-v1.1.0-compliance-review.md`
- Existing specs: `~/.hermes/specs/` (akan diaudit di Stage 5.0 step next)
- Existing implementation:
  - `/home/infra/projects/stella-agent/` (to deprecate, orchestration logic → STELLA profile)
  - `/home/infra/projects/kumachii-executor/` (reframe as EDISON reference library)
- Knowledge base: `~/.hermes/knowledge/` (shared KB, akan dikelola BONNEY di Stage D)

---

**Plan ini adalah plan of record. Next step: audit 7 file di ~/.hermes/specs/.**
