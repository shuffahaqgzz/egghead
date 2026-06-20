# KUMACHII-STELLA Framework v1.1.0 — Compliance Review & Implementation Roadmap

**Date:** 2026-05-12
**Reviewer:** Hermes (Opus 4.7 via 9Router)
**Subject:** Kajian ulang implementasi aktual vs framework reference v1.1.0
**Status:** Critical review + revised roadmap
**Purpose:** Dokumentasi gap analysis dan best practice untuk migrasi ke compliance

---

## Ringkasan Eksekutif

Implementasi saat ini (Stella Python service + Kumachii Python executor + Redis + persistent KB) **~25% aligned** dengan framework v1.1.0. Banyak hal yang sudah dikerjakan valuable sebagai MVP, tetapi **arah arsitektur divergen** dari yang framework mandate.

Phase 5 plan sebelumnya (wrap Python modules → Hermes skills dalam 1 profile) **salah arah** karena tidak cek framework dulu. Framework v1.1.0 menuntut multi-profile dedicated runtime, bukan skill-based single-profile architecture.

Dokumen ini berisi:
1. Kajian kritis framework v1.1.0
2. Komparasi vs state implementasi saat ini
3. Identifikasi 4 masalah konseptual kritis
4. Revised roadmap (Phase 5.0 → 5.5) untuk full compliance
5. 3 opsi strategis dengan tradeoff

---

## 1. Kajian Framework v1.1.0 (Reference Architecture)

### Kerangka Utama

- **10 agents** dengan role terpisah jelas:
  - **KUMACHII** — personal assistant + user-facing intake
  - **STELLA** — orchestrator, workflow state owner, project controller
  - **SHAKA** — architect (architecture, ADRs)
  - **EDISON** — coding (TDD executor)
  - **PYTHAGORAS** — research (read-only)
  - **ATLAS** — security review (can block release)
  - **YORK** — QA/reviewer (review-only + test execution)
  - **LILITH** — DevOps (deployment, gated)
  - **BONNEY** — documentation + RAG knowledge
  - **STUSSY** — monitoring + operations

- **Hermes-native**:
  - Setiap agent = dedicated profile (`~/.hermes/profiles/<agent>/`)
  - Systemd service per agent (`hermes-gateway-<agent>.service`)
  - Isolated credentials, memory, sessions, skills

- **Platform split**:
  - Telegram: eksklusif KUMACHII
  - Discord: semua specialist agent (unique token per profile)

- **6-phase lifecycle**:
  - Phase 0 Discovery
  - Phase 1 Planning
  - Phase 2 Solutioning
  - Phase 3 Implementation
  - Phase 4 Review
  - Phase 5 Deploy
  - Phase 6 Operate

- **Quality gates** wajib setiap phase transition (Gate 0-6) dengan decision: PASS / CONCERNS / FAIL

- **Handoff protocol** = markdown atau YAML antar agent (bukan JSON schema internal)

- **RAG + Memory**:
  - Memory per-agent isolated
  - Shared knowledge base via BONNEY
  - Source hierarchy: source-of-truth > ADRs > PRD > code > generated summary

- **Security**:
  - Least-privilege tool access
  - Isolated credentials (no shared bot tokens)
  - Human approval wajib untuk risky operations

---

## 2. Komparasi vs Implementasi Kerjaan Saat Ini

### Snapshot State Saat Ini

```txt
~/.hermes/profiles/          → kosong (cuma default profile)
/home/infra/projects/
├── stella-agent/            → standalone Flask Python service
└── kumachii-executor/       → standalone Python executor
~/.hermes/specs/             → 7 file spec ada
  ├── INDEX.md
  ├── KUMACHII.md
  ├── STELLA.md
  ├── MEMORY_MODEL.md
  ├── HANDOFF_PROTOCOL.md
  ├── DEPLOYMENT_GATE.md
  └── QUICK_REFERENCE.md
~/.hermes/knowledge/         → persistent KB (decision log, error patterns, metrics)
```

### Perbandingan Struktural

| Aspek | Framework v1.1.0 | Implementasi saat ini | Verdict |
|---|---|---|---|
| Jumlah agent | 10 dedicated | 2 (Stella + Kumachii) | Partial — sesuai rencana phased |
| Runtime | Hermes profile per agent | Standalone Python service | **Divergen** |
| Isolasi | Profile-level (creds/memory/session) | Single Redis + single service | **Divergen** |
| Kumachii role | Personal assistant + intake | Python executor (rename salah) | **Divergen** |
| Stella role | Orchestrator | OK (sesuai) | Aligned |
| Executor role | EDISON | Tidak ada, Kumachii menyamar jadi executor | **Salah konsep** |
| Platform | Telegram (KUMACHII) + Discord (specialist) | Flask HTTP | **Divergen** |
| Systemd | `hermes-gateway-<agent>.service` per agent | Tidak ada | **Divergen** |
| Workflow | 6-phase + Gate 0-6 | 3-tier routing | **Divergen** |
| Handoff | Markdown/YAML kontrak | JSON schema | Partial equivalent |
| Memory | Per-agent isolated + shared KB | Single Redis + disk KB | Partial |
| RAG | BONNEY dedicated + source hierarchy | Tidak ada | Missing |
| TDD/Quality gates | Wajib | Tidak ada enforcement | Missing |

---

## 3. Masalah Konseptual Kritis

### Masalah 1: Role Kumachii Salah

**Framework v1.1.0:**
```txt
KUMACHII = personal assistant + intake layer (user-facing)
- office work
- meeting notes
- content creation
- campus education
- finance organization
- intake untuk STELLA kalau kompleks
```

**Implementasi:**
```txt
Kumachii = Python executor
- investigate → plan → execute → verify phase
- tool execution (echo, build, test)
```

Yang dibuat di `kumachii-executor/` itu sebenarnya role **EDISON** (coding/execution agent), bukan KUMACHII.

**Konsekuensi:**
- Nama salah → architecture-level confusion
- Kalau Stage B tambah EDISON, akan ada 2 executor role (Kumachii + EDISON) yang overlap
- Gilang akan kebingungan kapan pakai Kumachii vs EDISON

---

### Masalah 2: Phase 5 Plan Sebelumnya Salah Arah

Plan sebelumnya: **wrap existing Python → Hermes skills dalam 1 profile**

Itu **anti-framework**. Framework v1.1.0 minta:

- 1 profile = 1 agent dedicated
- Credential isolation = safety requirement
- Systemd service per agent = production pattern
- Isolated memory per agent

Kalau semua skill di 1 profile, yang dibangun **bukan** multi-agent system — tapi cuma 1 agent dengan banyak prompt template.

**Akar masalah:** tidak baca framework v1.1.0 dulu sebelum bikin roadmap.

---

### Masalah 3: 3-Tier Routing ≠ 6-Phase Workflow

**Implementasi sekarang:**
```txt
Tier 1 → auto-execute
Tier 2 → ambiguity → ask approval
Tier 3 → production → mandatory approval
```

**Framework minta:**
```txt
Phase 0 Discovery (KUMACHII/STELLA)
Phase 1 Planning (STELLA)
Phase 2 Solutioning (SHAKA)
Phase 3 Implementation (EDISON)
Phase 4 Review (YORK + ATLAS)
Phase 5 Deploy (LILITH)
Phase 6 Operate (STUSSY)
+ Gate 0-6 PASS/CONCERNS/FAIL per transition
```

**Perbedaan fundamental:**
- Tier-based = **risk classification** (axis: seberapa berbahaya task)
- Phase-based = **workflow discipline** (axis: tahap mana di lifecycle)

Keduanya beda axis, **harus ada dua-duanya**. Saat ini cuma ada tier, phase dan gate tidak ada.

---

### Masalah 4: Spec Sendiri vs Framework

Spec yang ada di `~/.hermes/specs/` (MEMORY_MODEL.md, HANDOFF_PROTOCOL.md, dll) adalah **turunan dari workflow standalone-service**, bukan mapping dari framework v1.1.0.

**Gap:** Spec bilang A, framework bilang B. Akan ada konflik kalau tidak dirapikan.

**Contoh konkret:**
- Spec HANDOFF_PROTOCOL.md pakai JSON schema
- Framework Section 10 pakai markdown/YAML contract
- Untuk inter-agent handoff, framework wajib markdown (bisa di-commit ke `docs/`)

---

## 4. Best Practice Implementasi (Rekomendasi Revisi)

Kalau serius ikuti framework v1.1.0, berikut jalan yang benar.

### 4.1 Perbaikan Konseptual (FIRST)

Sebelum lanjut implementasi, rapikan konsep:

**Langkah 1: Rename & pisahkan**

```txt
SEKARANG                                  →  SEHARUSNYA
/home/infra/projects/
├── stella-agent/  (orchestrator)         →  DEPRECATE, atau keep as STELLA reference impl
└── kumachii-executor/  (executor)        →  RENAME → edison-executor/
                                              (Kumachii tidak di sini — dia intake-only)
```

**Langkah 2: Reframing Phase 5 plan**

```txt
Old plan (1 skill profile)        →  KILL
New plan (multi-profile dedicated) →  ADOPT
```

---

### 4.2 Implementasi Bertahap (Revised Roadmap)

Ini jalur yang benar — **bertahap + test per-phase**:

#### Stage A — Foundation (Minimum Viable, 2 agent Hermes-native)

**Goal:** Framework working untuk 2 agent, bukan standalone service.

```bash
# 1. Create Hermes profile untuk KUMACHII (intake only, bukan executor)
hermes profile create kumachii
# Setup:
# - model: balanced (Haiku 4.5 / Sonnet)
# - platform: Telegram (primary)
# - role file: copy docs/KUMACHII.md as SOUL.md

# 2. Create Hermes profile untuk STELLA (orchestrator)
hermes profile create stella
# Setup:
# - model: strongest reasoning (Opus 4.7)
# - platform: Discord
# - role file: copy docs/STELLA.md as SOUL.md

# 3. Setup systemd services
# Template di framework Section 13
systemctl --user enable hermes-gateway-kumachii.service
systemctl --user enable hermes-gateway-stella.service

# 4. Test isolation + handoff
kumachii chat -q "ping"
stella chat -q "ping"
```

**Exit criteria Stage A:**
- 2 profile aktif dengan systemd service sendiri
- Credential isolated (auth.json per profile)
- Telegram eksklusif KUMACHII, Discord untuk STELLA
- Handoff dari KUMACHII → STELLA via structured message working

---

#### Stage B — Build Layer (EDISON + PYTHAGORAS)

Setelah Stage A stabil 1-2 minggu:

```bash
hermes profile create edison      # coding agent (TDD executor)
hermes profile create pythagoras  # research agent

# Migrate Python executor logic jadi EDISON skill/prompt di profile edison
# Bukan rename nama — rewrite role from scratch ikut spec framework Section 7.4
```

**Key refactor:**
- Code di `/home/infra/projects/kumachii-executor/` jadi **reference library** yang dipanggil via skill di EDISON profile
- Bukan standalone service lagi

**Exit criteria Stage B:**
- EDISON bisa handle coding task via TDD cycle
- PYTHAGORAS bisa riset evidence-based dengan citation
- STELLA bisa delegate ke EDISON dan PYTHAGORAS via handoff markdown

---

#### Stage C — Control Layer (ATLAS + YORK)

```bash
hermes profile create atlas       # security review
hermes profile create york        # QA review
```

**Properties:**
- Kedua agent ini **review-only access**
- Tidak bisa modifikasi code
- Cuma bisa baca + kasih report
- ATLAS can block release untuk security issue
- YORK can block release untuk failed acceptance criteria

**Exit criteria Stage C:**
- ATLAS validate EDISON output (no hardcoded secrets, no unsafe dependencies)
- YORK validate test coverage dan acceptance criteria
- Block mechanism working (STELLA tidak bisa approve deploy kalau ATLAS/YORK fail)

---

#### Stage D — Operate Layer (LILITH + BONNEY + STUSSY)

```bash
hermes profile create lilith      # DevOps (deployment gated)
hermes profile create bonney      # docs + RAG
hermes profile create stussy      # monitoring
```

**Exit criteria Stage D:**
- LILITH bisa deploy staging (auto) dan production (dengan approval)
- BONNEY maintain RAG knowledge base
- STUSSY monitor health + generate runbook

---

#### Stage E — Architecture Layer (SHAKA)

Terakhir, SHAKA sebagai architect:

```bash
hermes profile create shaka
```

**Exit criteria Stage E:**
- SHAKA bisa create architecture document + ADRs
- Implementation readiness check working
- Full 10-agent deployment complete

---

### 4.3 Memory Model Adjustment

Dari framework Section 18, memory **per-agent isolated**, bukan single Redis:

```txt
SEKARANG (centralized):
Redis (port 6379) ← semua agent share

SEHARUSNYA (per-profile + shared KB):
~/.hermes/profiles/kumachii/memory/    → personal preferences
~/.hermes/profiles/stella/memory/      → workflow state
~/.hermes/profiles/edison/memory/      → implementation patterns
~/.hermes/profiles/pythagoras/memory/  → research notes
~/.hermes/profiles/atlas/memory/       → security policies
~/.hermes/profiles/york/memory/        → QA checklists
~/.hermes/profiles/lilith/memory/      → deployment references
~/.hermes/profiles/bonney/memory/      → documentation standards
~/.hermes/profiles/stussy/memory/      → operational baselines
~/.hermes/profiles/shaka/memory/       → architecture decisions

~/.hermes/knowledge/                   → shared KB (managed by BONNEY)
```

**Redis role revised:**
- Dipakai **hanya untuk STELLA workflow state**
- Bukan global session cache
- Bukan shared memory antar agent

---

### 4.4 Handoff Protocol Migration

Dari JSON schema (saat ini) ke **markdown contract** (framework Section 10):

```markdown
# Agent Handoff

From: KUMACHII
To: STELLA
Task ID: TASK-0001
Phase: discovery
Priority: medium
Objective: Plan personal finance dashboard

Inputs:
- User request via Telegram

Files touched:
- docs/intake/task-brief.md

Decisions made:
- None yet (discovery phase)

Assumptions:
- Single user (personal use)
- Web-based dashboard

Constraints:
- No new paid dependencies
- Must work offline

Open risks:
- Data privacy for financial info

Acceptance criteria:
- Scope clear
- Risks identified
- Research complete

Required review:
- PYTHAGORAS (research)
- ATLAS (security threat model)

Next action:
- STELLA opens Phase 0
- Delegate research to PYTHAGORAS

Status: PASS
```

**Keunggulan markdown vs JSON:**
- Human-readable (Gilang bisa audit langsung)
- Langsung jadi dokumen di `docs/intake/` atau `docs/epics/`
- Git-trackable
- Tidak butuh custom parser
- Bisa di-render di Telegram/Discord langsung

**Kapan JSON cocok:**
- API internal antar service (bukan handoff antar agent)
- Machine-to-machine serialization
- Tidak untuk kontrak inter-agent (ini markdown)

---

### 4.5 Quality Gates Enforcement

Yang hilang total di implementasi saat ini: **Gate 0-6**.

**Implementation requirement:**

- Tiap phase transition butuh gate checklist
- STELLA owner gate enforcement
- Gate failure = block next phase
- Gilang = final approver untuk gate PASS

**Minimum implementation:**

File gate di `docs/gates/gate-<N>-<task-id>.md`, diisi STELLA, approve oleh Gilang.

Template (framework Section 19):

```markdown
# Gate 0 — Discovery Approved

- [ ] Problem is clear
- [ ] Goal is clear
- [ ] Scope is bounded
- [ ] Out-of-scope is listed
- [ ] Assumptions are listed
- [ ] Key terms are defined
- [ ] Owner and reviewer assigned

Decision: PASS / CONCERNS / FAIL
Reviewer: Gilang
Date: 2026-05-12
Notes:
```

**Rule:**
- Gate FAIL = task stuck di phase itu, tidak lanjut
- Gate CONCERNS = lanjut tapi dengan monitoring
- Gate PASS = lanjut ke phase berikutnya

---

### 4.6 Urutan Rekomendasi (Revisi Phase 5 Lengkap)

**Kill old Phase 5 plan. Ganti dengan ini:**

```txt
Phase 5.0 Conceptual fix (1 day)
  - Rename kumachii-executor → edison-executor
  - Update spec docs untuk align sama framework v1.1.0
  - Deprecate standalone Flask jadi optional fallback
  - Document framework compliance plan

Phase 5.1 Hermes foundation (2-3 days)
  - Create kumachii + stella profiles (Section 11 framework)
  - Setup systemd services (Section 13)
  - Test isolation (Section 24)

Phase 5.2 Migrate logic (3-5 days)
  - Convert kumachii-executor → EDISON profile skill
  - Convert stella-agent orchestration → STELLA profile SOUL.md
  - Redis jadi STELLA state store (bukan global)

Phase 5.3 Handoff + Gates (2-3 days)
  - Implement markdown handoff (Section 10)
  - Implement Gate 0-6 templates (Section 19)
  - Test end-to-end KUMACHII → STELLA → EDISON flow

Phase 5.4 Build Layer expansion (1 week)
  - Add EDISON + PYTHAGORAS profile
  - Test Stage B exit criteria

Phase 5.5 Control + Operate + Architect (2-3 weeks)
  - Add ATLAS, YORK, LILITH, BONNEY, STUSSY, SHAKA
  - Full framework deployment
```

**Total estimasi:** ~4-5 minggu untuk full framework compliance, bukan 1 weekend.

---

## 5. Tiga Opsi Strategis

### Opsi A — Full Framework Compliance (Rekomendasi kalau serius)

**Scope:**
- Drop standalone Python service architecture
- Rewrite sebagai Hermes multi-profile
- Ikuti framework v1.1.0 sepenuhnya

**Timeline:** 4-5 minggu

**Keunggulan:**
- Arsitektur correct, scalable
- Isolation + safety sesuai production pattern
- Dokumentasi match implementasi
- Future-proof untuk 10 agent scale

**Kelemahan:**
- Investment besar upfront
- Rewrite existing work (not all code wasted, jadi reference library)
- Butuh disiplin eksekusi konsisten

---

### Opsi B — Hybrid Pragmatic (Tradeoff)

**Scope:**
- Keep standalone Python service sebagai "backend library"
- Frontend via Hermes profile (minimum 2 agent: KUMACHII + STELLA)
- Scale ke 10 agent kalau perlu

**Timeline:** 1-2 minggu untuk minimum, expand as needed

**Keunggulan:**
- Ship cepat
- Existing code tidak wasted
- Bisa validate concept dulu sebelum commit full framework

**Kelemahan:**
- Arsitektur hybrid = maintenance double (Python service + Hermes profile)
- Tidak fully match framework
- Risk of stuck di "pragmatic middle" tanpa pernah migrate penuh

---

### Opsi C — Keep Current, Acknowledge Divergence

**Scope:**
- Tidak ubah apa-apa
- Implementasi saat ini tidak akan jadi multi-agent sistem sesuai framework
- Harus deprecate framework v1.1.0 sebagai reference ATAU tulis framework v2.0.0 yang match implementasi

**Timeline:** 0 (no change)

**Keunggulan:**
- Tidak ada rewrite
- Sistem existing keep running

**Kelemahan:**
- Tidak multi-agent architecture sesungguhnya
- Framework v1.1.0 jadi stranded reference
- Tidak ada path ke 10-agent deployment
- Scale limit di 2-3 agent di single process

---

## 6. Verdict Final

**Implementasi saat ini: ~25% aligned dengan framework v1.1.0.**

**Gap utama:**
- Runtime architecture (standalone Python vs Hermes profile)
- Role definition (Kumachii salah jadi executor)
- Workflow model (tier vs phase + gate)
- Isolation model (centralized Redis vs per-agent memory)
- Handoff format (JSON vs markdown contract)

**Phase 5 plan sebelumnya salah arah.** Tidak cek framework dulu. Revisi mandatory.

**Rekomendasi:**
- **Opsi A** kalau commit long-term ke framework (rekomendasi utama)
- **Opsi B** kalau butuh ship cepat tapi mau eventually migrate
- **Opsi C** kalau scope beneran cuma 2-agent system (bukan framework)

---

## 7. Langkah Konkret Berikutnya

1. **Decide:** Opsi A, B, atau C
2. **If A atau B:**
   - Backup existing code di `/home/infra/projects/stella-agent/` dan `kumachii-executor/`
   - Start Phase 5.0 (Conceptual fix)
   - Review dokumen ini + framework v1.1.0 + existing spec untuk ketiganya align
3. **If C:**
   - Deprecate `~/.hermes/knowledge/topics/kumachii-stella-multi-agent-workflow-framework-v1.1.0-final.md`
   - Rewrite sebagai framework v2.0.0 yang match implementasi saat ini

---

## Referensi

- **Framework source:** `~/.hermes/knowledge/topics/kumachii-stella-multi-agent-workflow-framework-v1.1.0-final.md`
- **Existing spec:** `~/.hermes/specs/` (INDEX.md, KUMACHII.md, STELLA.md, MEMORY_MODEL.md, HANDOFF_PROTOCOL.md, DEPLOYMENT_GATE.md, QUICK_REFERENCE.md)
- **Existing implementation:**
  - `/home/infra/projects/stella-agent/`
  - `/home/infra/projects/kumachii-executor/`
- **Knowledge base:** `~/.hermes/knowledge/` (DECISION_LOG, ERROR_PATTERNS, TASK_HISTORY, AGENT_PERFORMANCE, COST_TRACKING)

---

**Dokumen ini harus dibaca bareng framework v1.1.0 untuk konteks penuh.**
