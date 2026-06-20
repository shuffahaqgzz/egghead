# Spec Audit — `~/.hermes/specs/` vs Framework v1.1.0

**Date:** 2026-05-13
**Author:** Hermes
**Status:** Complete (Phase 5.0 step 2/9)
**Scope:** Audit 7 spec files di `~/.hermes/specs/` terhadap framework v1.1.0. Setiap file dapat verdict: **DEPRECATE**, **REVISE**, atau **RETAIN** + migration notes.

---

## 0. Ringkasan

| # | File | Size | Verdict | Target State |
|---|---|---|---|---|
| 1 | `INDEX.md` | 20 KB / 541 L | **REVISE** | Ganti jadi index 10-agent framework, bukan 2-agent |
| 2 | `KUMACHII.md` | 21 KB / 799 L | **REPURPOSE → EDISON.md** | Role-nya Edison (executor), bukan Kumachii |
| 3 | `STELLA.md` | 18 KB / 641 L | **REVISE (hard)** | Adaptasi ke framework Section 7.2 (orchestrator + 7-phase + Gate 0-6) |
| 4 | `MEMORY_MODEL.md` | 11 KB / 286 L | **REVISE** | Single Redis → per-profile memory; STELLA-scoped state |
| 5 | `HANDOFF_PROTOCOL.md` | 28 KB / 1076 L | **DEPRECATE** | JSON → markdown contract (Section 10 framework) |
| 6 | `DEPLOYMENT_GATE.md` | 19 KB / 672 L | **REVISE** | 3-tier → Gate 5 + risk metadata; fold ke LILITH + ATLAS |
| 7 | `QUICK_REFERENCE.md` | 10 KB / 372 L | **DEPRECATE** | Obsolete setelah INDEX.md direvisi |

**Total yang harus di-rewrite / removed:** 7 dari 7. Tidak ada yang survive as-is. Bisa dipertahankan **nilai struktural** (checklist investigation, planning, verification) tapi harus di-re-home ke file/role yang benar.

Naming baru setelah migrasi (target di Phase 5.3):

```
~/.hermes/specs-framework-v1.1.0/
├── INDEX.md                    (rewrite — 10 agent)
├── AGENTS.md                   (new — master operating guide, Section 21)
├── KUMACHII.md                 (new — intake/personal assistant, Section 7.1)
├── STELLA.md                   (rewrite — orchestrator, Section 7.2)
├── SHAKA.md                    (new — Section 7.3)
├── EDISON.md                   (rewrite dari old KUMACHII.md, Section 7.4)
├── PYTHAGORAS.md               (new — Section 7.5)
├── ATLAS.md                    (new — Section 7.6)
├── YORK.md                     (new — Section 7.7)
├── LILITH.md                   (new — Section 7.8 + absorb DEPLOYMENT_GATE)
├── BONNEY.md                   (new — Section 7.9 + RAG)
├── STUSSY.md                   (new — Section 7.10)
├── HANDOFF_PROTOCOL.md         (rewrite markdown — Section 10)
├── MEMORY_MODEL.md             (rewrite per-profile — Section 18)
├── RAG_ARCHITECTURE.md         (new — Section 16)
├── QUALITY_GATES.md            (new — Section 19, Gate 0-6 templates)
└── WORKFLOW_LIFECYCLE.md       (new — Section 8, Phase 0-6)
```

Old `~/.hermes/specs/` di-archive (rename → `~/.hermes/specs-legacy-2026-05/`) setelah migrasi lengkap. **Tidak dihapus** — masih valuable sebagai rujukan keputusan desain MVP Phase 1-4.

---

## 1. File-by-File Audit

### 1.1 `INDEX.md` — **REVISE**

**Isi sekarang:**
- Overview 2-agent system (Kumachii + Stella) + memory/handoff/deployment gate
- System architecture diagram dengan 2 agent
- 3 workflow example scenarios (deploy staging, deploy prod, ambiguous)
- Implementation roadmap Phase 1-4 MVP

**Masalah vs framework v1.1.0:**
- Arsitektur 2-agent, framework 10-agent
- Role Kumachii salah (dia executor, framework-nya personal assistant)
- Tidak mention 6-layer foundation, 7-phase, Gate 0-6
- Diagram architecture tidak match Section 4 framework
- Implementation roadmap obsolete (Phase 5.0 plan baru menggantikan)

**Yang masih valuable:**
- Struktur dokumen (overview → architecture → scenarios → roadmap)
- Konsep "Definition of Done" yang konkret
- Success metrics table pattern
- Workflow example format (T=0:00 trace)

**Target revisi:**
- Ganti jadi index 10 spec files + 17 total doc (lihat naming baru di Bagian 0)
- Diagram arch ganti ke Section 4 framework
- Workflow example tetap ada tapi 10-agent, pakai finance dashboard dari Section 29 framework
- Roadmap ganti jadi Stage A-E dari Phase 5.0 plan

**Action:** Revisi pada Phase 5.3 setelah semua file baru ditulis.

---

### 1.2 `KUMACHII.md` — **REPURPOSE → EDISON.md**

**Isi sekarang:**
- Agent = executor (investigator, planner, executor, verifier)
- Investigation checklist detail (9 item)
- Planning phase dengan template multi-phase estimates
- Execution phase dengan parallel/sequential discipline
- Verification phase dengan 10-item checklist
- Decision making (allowed vs not allowed)
- Communication reporting format
- Memory & context section (session + persistent KB query)
- Quality gates (build, test, lint)

**Masalah vs framework v1.1.0:**
- **Role salah fundamental.** Ini semua konten Edison (Section 7.4 — coding agent TDD executor).
- Di framework v1.1.0, Kumachii = personal assistant + intake (Section 7.1) — jauh beda.
- Skill Investigation + Planning + Execute + Verify adalah 4-phase mini-lifecycle yang cocok untuk **per-story work Edison**, bukan untuk end-user chat Kumachii.

**Yang masih valuable** (harus diselamatkan → EDISON.md):
- Investigation checklist (wajib sebelum implementasi story)
- Planning template dengan phase breakdown + estimates
- Parallel vs sequential tool-calling discipline (Layer 3 OMO)
- Verification checklist 10-item (Gate 3 input)
- Decision template JSON (bisa dipakai di handoff decisions_made section)
- Reporting format terse (match Layer 5 token optimization)

**Yang perlu dihapus:**
- Anything implying Kumachii = executor (semua)
- "Kumachii receives task from Stella" — direverse: Kumachii receives from user, escalate ke Stella kalau kompleks
- Decision tree error handling yang tidak relevan untuk Edison scope

**Yang perlu ditambah untuk jadi EDISON.md:**
- TDD cycle wajib (RED → GREEN → REFACTOR → VERIFY → REVIEW) dari Layer 1
- Architecture boundary respect (Section 7.4 "Must not redesign architecture")
- Approval request untuk dependency baru (Section 7.4)
- Atomic commit rule
- Handoff ke YORK (Section 7.4 last line)

**Kumachii baru** (SOUL.md di profile kumachii):
- Ditulis fresh, bukan dari file ini
- Ikut Section 7.1 framework
- Role: personal assistant office/content/education/finance
- Intake layer buat Stella kalau kompleks

**Action:** 
1. Copy `KUMACHII.md` → `specs-legacy-2026-05/KUMACHII.md` (arsip)
2. Rewrite as `EDISON.md` dengan refactor ke role coding TDD
3. Tulis fresh `KUMACHII.md` baru (personal assistant)

---

### 1.3 `STELLA.md` — **REVISE (hard)**

**Isi sekarang:**
- Role: orchestrator, delegation hub, approval gateway, state manager
- Task routing matrix 3-tier (low/medium/high risk)
- Delegation matrix Stella → Kumachii + Stella → User
- Handoff JSON schema (task + result)
- Escalation & approval template
- 4-phase handoff protocol (User → Stella → Kumachii → Stella → User)
- Approval gate sync wait mechanism
- State management & sync (session ↔ persistent)
- Communication style to user
- Implementation roadmap v1-v1.2

**Yang sejalan framework** (survive):
- Stella owner orchestration (Section 7.2) ✓
- Stella NOT execute, route ke specialist ✓
- Approval gate pattern ✓
- Escalation ke user untuk ambiguity ✓
- Decision logging ✓
- Terse communication style ✓

**Yang harus direvisi:**
- **Delegation matrix 2-agent → 10-agent** (Section 9 framework)
- **3-tier routing → 7-phase + Gate 0-6** (Section 8 framework). Tier bisa survive sebagai **risk metadata** di handoff, tapi primary axis adalah phase
- **Handoff JSON → markdown** (Section 10 framework)
- **Session Redis single → per-profile memory + STELLA state store** (Section 18)
- **Approval flow diperluas**: tidak cuma production deploy, tapi tiap gate transition berisiko (Gate 2 architecture, Gate 4 review, Gate 5 deploy)
- Role "state manager" perlu limited scope — workflow state only, bukan shared memory across agents

**Yang harus ditambahkan:**
- Gate 0-6 enforcement (PASS/CONCERNS/FAIL templates dari Section 19)
- Phase ownership rules (Phase 0 co-owned KUMACHII, Phase 1 STELLA, Phase 2 SHAKA, etc.)
- Can block rules: ATLAS/YORK/STUSSY bisa block release (Stella enforce)
- Human approval checkpoints per phase sesuai Delegation Matrix Section 9

**Struktur target:**
```
STELLA.md (new)
1. Role (Section 7.2)
2. 7-Phase ownership matrix
3. Gate enforcement (import dari QUALITY_GATES.md)
4. Delegation matrix 10-agent (Section 9)
5. Handoff generation (reference ke HANDOFF_PROTOCOL.md markdown version)
6. Risk metadata handling (lama 3-tier, sekarang metadata)
7. State management (workflow state only, per-profile memory tidak dikelola Stella)
8. Escalation rules (Section 21 framework)
9. Communication style
10. Boundary rules (must not: bypass review, self-approve, deploy prod, skip phase)
```

**Action:** Rewrite full, preserve hanya pattern + terse communication style.

---

### 1.4 `MEMORY_MODEL.md` — **REVISE**

**Isi sekarang:**
- Layer 1: Session-based (Redis, per-task, 24h TTL)
- Layer 2: Persistent (disk + git, cross-session)
- Redis schema detail (task:{id}:state, execution.*, intermediate.*, approval.*, result.*)
- Persistent schema di `~/.hermes/knowledge/`
- Sync protocol automatic + manual
- Query API pseudocode
- Best practice matrix (session vs persistent by use case)

**Masalah vs framework v1.1.0 Section 18:**
- **Centralized Redis** vs **per-profile memory**. Framework wajib per-agent isolation.
- Persistent KB sekarang single tree `~/.hermes/knowledge/` — framework masih OK dengan ini untuk **shared KB**, tapi per-agent memory harus pindah ke `~/.hermes/profiles/<agent>/memory/`
- Redis role berlebihan — di framework, Redis hanya untuk STELLA workflow state, bukan global session cache
- Tidak mention BONNEY sebagai owner shared KB

**Pemetaan lama → baru:**

| Lama | Baru | Catatan |
|---|---|---|
| Redis `task:{id}:*` global | Redis STELLA-only workflow state | STELLA profile punya Redis instance sendiri atau namespaced |
| `~/.hermes/knowledge/global/` | `~/.hermes/knowledge/global/` (kept) | Shared KB, owner BONNEY |
| `~/.hermes/knowledge/projects/` | `~/.hermes/knowledge/projects/` (kept) | Shared KB per project |
| `~/.hermes/knowledge/agents/` | `~/.hermes/profiles/<agent>/memory/` | **Moved** ke per-profile |
| Session `intermediate.vars.*` | Session per-profile (Hermes native session store) | Tidak perlu Redis cache lagi |
| `mark_for_persist` API | BONNEY ingest via manifest | Formalized di RAG pipeline |

**Yang perlu ditambah:**
- Source-of-truth hierarchy (Section 16.1) — authority level 1-8
- Per-agent memory scope per Section 18.2
- "Do not auto-share sensitive memory" rule
- Handoff documents sebagai default sharing mechanism

**Action:** Rewrite dengan struktur:
```
MEMORY_MODEL.md (new)
1. Three memory tiers
   a. Per-profile memory (~/.hermes/profiles/<agent>/memory/) — private
   b. Shared knowledge base (~/.hermes/knowledge/) — BONNEY-curated
   c. Workflow state (STELLA Redis) — workflow-scoped
2. Source authority hierarchy (Section 16.1)
3. Memory scope per agent (Section 18.2 full table)
4. Sync protocols
5. Handoff as sharing mechanism
6. Query API
7. Do-nots (no auto-share sensitive, no cross-agent memory bleed)
```

---

### 1.5 `HANDOFF_PROTOCOL.md` — **DEPRECATE**

**Isi sekarang:**
- User → Stella natural language parsing
- Stella → Kumachii JSON schema (task)
- Kumachii → Stella JSON schema (result)
- Approval request JSON schema
- User response parsing
- Escalation/clarification format
- Error handling & recovery
- 7 detailed JSON examples (deploy, rollback, research, cost, etc)
- Redis session schema reference
- Task ID generation pattern

**Masalah fundamental:**
- Framework Section 10 mandate **markdown atau YAML handoff**, bukan JSON
- Framework Section 3.3 "Documents as Contracts" — handoff = dokumen yang bisa di-git-commit + audit
- JSON schema bagus untuk M2M API, tapi untuk **inter-agent handoff**, markdown superior (human-readable, git-trackable, bisa di-render di Telegram/Discord)

**Yang masih valuable** (slice out sebagai reference, jangan blanket deprecate):
- Pattern 7 detailed examples (deploy, rollback, research, cost) — konsep skenario survive, format diganti markdown
- Approval SLA matrix (2h emergency / 4h normal / 24h strategic) — pindah ke DEPLOYMENT_GATE revision (jadi LILITH.md)
- Task ID pattern `task_YYYYMMDD_NNN` — survive
- Error handling state machine

**Yang dideprecate penuh:**
- JSON schema definitions (ganti markdown template Section 10)
- Redis session schema reference (pindah ke MEMORY_MODEL.md new)
- Natural language parsing detail (pindah ke KUMACHII.md new)

**Action:** 
1. Archive `HANDOFF_PROTOCOL.md` → `specs-legacy-2026-05/`
2. Tulis baru `HANDOFF_PROTOCOL.md` v2.0 sesuai Section 10 framework:
   - Markdown handoff template (dari Section 10)
   - YAML variant
   - Rules (no vague handoff, no missing paths, blocked → STELLA)
   - 7 example scenarios **dalam markdown**, adapt dari JSON yang lama
   - File location convention: `docs/intake/task-brief-<id>.md`, `docs/epics/epic-<id>.md`, `docs/reviews/review-<id>.md`

---

### 1.6 `DEPLOYMENT_GATE.md` — **REVISE**

**Isi sekarang:**
- Core principle "User Approval Always"
- Tier 1/2/3 classification
- 48h staging validation requirement
- Production deployment process (pre-flight, approval, execution, monitoring)
- Approval request JSON structure
- Deployment execution blue-green
- Rollback procedure
- Post-deploy monitoring 1h
- Emergency procedures
- Audit trail + runbooks

**Yang sejalan framework** (survive):
- User approval mandatory untuk production (Section 8 Phase 5) ✓
- Staging validation sebelum production ✓ (Section 7.8 LILITH)
- Rollback plan ready sebelum deploy (Section 8 Gate 5) ✓
- Post-deploy monitoring ✓ (Section 7.10 STUSSY)
- Emergency / security hotfix exception dengan approval explicit ✓

**Yang harus direvisi:**
- **Tier 1/2/3 axis → Gate 5 phase axis**. Tier-nya bisa survive sebagai risk metadata di handoff (field `risk_level: low/medium/high`), tapi gating mechanism = Gate 5 dengan PASS/CONCERNS/FAIL
- **Owner** ganti dari "Stella" ke LILITH (Section 7.8); Stella adalah reviewer Gate 5, bukan executor
- ATLAS review mandatory sebelum deploy (Section 4 tabel bisa block release) — tambahkan di pre-flight
- STUSSY monitoring ready sebelum deploy (Section 7.10 can block release) — tambahkan gate check

**Penamaan baru:**
- Content-nya pindah ke **LILITH.md** (primary owner deploy)
- Section 19 Gate 5 template dari framework jadi authoritative source
- Blue-green + rollback procedure tetap, tapi doc-nya di `docs/deployment.md` + `docs/runbook.md` per Section 20 project file structure

**Yang perlu ditambah:**
- Cross-reference ATLAS security review wajib
- Cross-reference STUSSY monitoring ready check
- Cross-reference YORK acceptance criteria pass
- Commissioning report template (Section 7.8 outputs)

**Action:**
1. Archive old `DEPLOYMENT_GATE.md` → `specs-legacy-2026-05/`
2. Content penting pindah ke `LILITH.md` (new)
3. Gate 5 authoritative template masuk ke `QUALITY_GATES.md` (new)
4. Operational procedure pindah ke template project `docs/deployment.md` + `docs/runbook.md`

---

### 1.7 `QUICK_REFERENCE.md` — **DEPRECATE**

**Isi sekarang:**
- Ringkasan 6 spec files (apa yang dibuat)
- Quick reference Kumachii + Stella responsibility
- Classification matrix (tier routing)
- Memory architecture summary
- Deployment gate rules (5 rules ringkas)
- Communication style

**Masalah:**
- Summary dari spec v1.0 yang sudah deprecated
- Tier matrix obsolete (ganti phase + gate)
- 2-agent framing obsolete

**Yang survive (pattern, bukan content):**
- Pattern "Quick Reference" document yang kondens 10 spec jadi 1 file
- Rule-based summary (5 rules clearly enumerated)

**Action:** Deprecate file lama. Tulis baru `QUICK_REFERENCE.md` setelah 10 spec baru ada (Phase 5.3 end). Format:
- Quick role reference 10 agent (1-liner each)
- Phase flow (Phase 0 → 6 with owner)
- Gate pass criteria summary
- Handoff template (minimum fields)
- Memory scope summary
- Common commands (hermes profile, systemd, chat test)

---

## 2. Migration Order

Urutan rewrite/buat file baru (di Phase 5.3):

1. **`WORKFLOW_LIFECYCLE.md`** — first, karena banyak file lain ref ke ini
2. **`QUALITY_GATES.md`** — second, WORKFLOW_LIFECYCLE ref ke templates ini
3. **`HANDOFF_PROTOCOL.md` v2** — third, agent contracts ref ke ini
4. **10 agent contracts** (`KUMACHII.md`, `STELLA.md`, ..., `STUSSY.md`) — parallel
5. **`MEMORY_MODEL.md` v2** — after agent contracts (agent scope tabel finalized)
6. **`RAG_ARCHITECTURE.md`** — after BONNEY.md defined
7. **`AGENTS.md`** — master operating guide, compile dari Section 21 framework + tweaks
8. **`INDEX.md` v2** — last, karena index ke semua file baru
9. **`QUICK_REFERENCE.md` v2** — last, summary pattern

---

## 3. Archive Policy

Old specs **tidak dihapus**. Di-archive dengan rename:

```bash
mv ~/.hermes/specs/ ~/.hermes/specs-legacy-2026-05/
mkdir ~/.hermes/specs-framework-v1.1.0/   # file baru di sini
```

Alasan preserve:
- Reference keputusan desain MVP Phase 1-4
- Audit trail (bisa cek apa yang berubah dan kenapa)
- Mitigasi risk (bisa rollback kalau framework v1.1.0 migration ada masalah tidak terduga)

Tambahkan README di archive:
```markdown
# specs-legacy-2026-05

Archived 2026-05-13 saat migrasi ke framework v1.1.0.

Active specs sekarang di: ~/.hermes/specs-framework-v1.1.0/
Audit dokumen: ~/.hermes/knowledge/topics/framework-v1.1.0/SPEC_AUDIT.md
```

---

## 4. Decision Summary

| Keputusan | Rasionalisasi |
|---|---|
| Semua 7 file butuh rework | Sebab framework v1.1.0 beda fundamental (10 agent, 7 phase, gate-based, markdown handoff) |
| Rename `KUMACHII.md` lama → `EDISON.md` | Role actual-nya coding executor, bukan intake |
| JSON handoff deprecated | Framework Section 10 mandate markdown/YAML |
| Single Redis deprecated | Framework Section 18 mandate per-profile memory |
| Tier-axis tidak dihapus total | Jadi risk metadata di handoff, tapi bukan primary gating |
| Archive old, no delete | Audit trail + rollback insurance |

---

**Next step Phase 5.0:** Buat `MIGRATION_MAPPING.md` (konkret old→new per komponen).
