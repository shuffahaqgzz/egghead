# Agent Skills Mandate Audit — Kumachii-Stella Framework

**Date:** 2026-05-18
**Auditor:** Hermes (default profile)
**Scope:** Semua 10 profile non-default — atlas, bonney, edison, kumachii, lilith, pythagoras, shaka, stella, stussy, york
**Question:** Apakah masing-masing agent secara strict sudah menggunakan skills (di-mandate via SOUL.md prinsip)?
**Verdict:** **TIDAK.** Skills tersedia di disk tapi **tidak di-mandate** sebagai prinsip operasional.

---

## TL;DR

| Aspek | Status |
|---|---|
| Skill bundle ada di `~/.hermes/profiles/<name>/skills/` | [OK] 89-100 skill per profile |
| SOUL.md mandate `WAJIB load skill X kalau Y` | [FAIL] hanya 1 skill (`handoff-template`) di 9/10 profile |
| Role-specific skill (mis. `stella-gate-enforcer`) disebut di SOUL.md | [FAIL] 0 mention di semua profile |
| KUMACHII (intake layer) menyebut skill apa pun | [FAIL] 0 mention di 75 baris SOUL.md |
| Pola seperti default profile (`WAJIB load skill `9router` SEBELUM ...`) | [FAIL] tidak ada pola serupa |

**Konsekuensi:** Agent harus menebak / hub-search / discover skill sendiri saat runtime. Skill mahal yang dibikin khusus per role (mis. `stella-orchestration`, `edison-atomic-commits`, `pythagoras-research-report`) **kemungkinan besar tidak pernah di-load** karena agent ngga tahu mereka ada.

---

## Tabel Audit Per Profile

| Profile | SOUL Lines | Skills di Disk | Skill Mention di SOUL | Role-Specific Skills (di disk, NOT mandated) |
|---|---|---|---|---|
| **kumachii** | 75 | 98 | **0 (none)** | `kumachii-intake`, `kumachii-content`, `kumachii-finance`, `kumachii-handoff`, `multi-agent-orchestration`, `multi-spec-orchestration` |
| **stella** | 261 | 100 | 1 (`handoff-template`) | `stella-orchestration`, `stella-decision-log`, `stella-delegation-router`, `stella-gate-enforcer`, `stella-redis-state`, `gate-templates`, `gate-checklist`, `edison-tdd-pipeline`, `qa-review`, `deployment-check`, `multi-spec-orchestration` |
| **edison** | 208 | 92 | 1 (`handoff-template`) | `edison-atomic-commits`, `edison-story-impl`, `tdd`, `test-driven-development`, `systematic-debugging` |
| **shaka** | 240 | 89 | 1 (`handoff-template`) | `architecture-diagram`, `excalidraw`, `writing-plans`, `plan` |
| **atlas** | 280 | 90 | 1 (`handoff-template`) | `security-review`, `github-code-review` |
| **york** | 258 | 92 | 1 (`handoff-template`) | `qa-review`, `security-review`, `tdd`, `requesting-code-review`, `github-code-review` |
| **lilith** | 223 | 91 | 1 (`handoff-template`) | `deployment-check`, `security-review`, `webhook-subscriptions`, `health-watchdog-cronjob` |
| **bonney** | 239 | 90 | 1 (`handoff-template`) | `rag-ingestion`, `obsidian`, `notion`, `nano-pdf`, `ocr-and-documents` |
| **pythagoras** | 238 | 91 | 1 (`handoff-template`) | `pythagoras-comparison`, `pythagoras-research-report`, `arxiv`, `blogwatcher`, `polymarket`, `llm-wiki` |
| **stussy** | 251 | 91 | 1 (`handoff-template`) | `health-watchdog-cronjob`, `deployment-check`, `webhook-subscriptions`, `kanban-worker` |

---

## Lokasi & Pola Skill Mention yang Ada Sekarang

Setiap profile (kecuali kumachii) hanya menyebut `handoff-template` 1× di section "Report-Back Pattern":

```markdown
**Rule:** Every Phase report you post back to STELLA's channel
(`1502667343279423619`) MUST use **file-based pattern**. NEVER dump
1500-2000 char single-message report into Discord channel. Load skill
`handoff-template` for full reference.
```

Itu satu-satunya `Load skill X` mandate yang strict di seluruh prinsip. Sisanya cuma list di bagian "Tools Available":

```markdown
- `memory`, `session_search`, `skills`, `todo`, `clarify`
```

— ini menyebut **tool** `skills` (akses ke skill registry), bukan skill spesifik.

---

## Gap Analysis — Yang HILANG di SOUL.md

1. **Tidak ada section "Skills (Mandatory)"** seperti default profile, yang list trigger conditions.
2. **Tidak ada mandate `WAJIB load skill X SEBELUM ...`** seperti pattern `9router` di default profile.
3. **Skill role-specific (yang sudah di-bundle) tidak pernah disebut**:
   - STELLA punya `stella-gate-enforcer` di disk → SOUL.md ngga pernah mention saat bahas Gate Enforcement.
   - EDISON punya `edison-atomic-commits` di disk → SOUL.md ngga pernah mention saat bahas atomic commits.
   - PYTHAGORAS punya `pythagoras-research-report` → ngga di-mandate saat bahas research output.
   - YORK punya `qa-review` → ngga di-mandate saat bahas review.
   - ATLAS punya `security-review` → ngga di-mandate saat bahas threat modeling.
4. **KUMACHII total kosong** — 75 baris SOUL.md tidak mention skill sama sekali, padahal ada 6 skill khusus di disk (`kumachii-intake`, dll).

---

## Kontras dengan Default Profile (sebagai baseline)

Default profile (Hermes/Engineering) punya pola eksplisit:

> **WAJIB load skill `9router` SEBELUM** menyentuh apa pun di `http://127.0.0.1:20128`

Pola ini yang HILANG di 10 profile lain. Tidak ada `WAJIB load skill X SEBELUM Y` per role.

---

## Rekomendasi (Patch Plan)

Tambahkan section **"Skills (Mandatory)"** ke setiap SOUL.md sebelum section "Behavior Rules".

### Template per role

#### KUMACHII (75 → ~110 lines)
```markdown
## Skills (MANDATORY)

Sebelum eksekusi, load skill yang sesuai trigger:
- Setiap intake klasifikasi → `multi-agent-framework/kumachii-intake`
- Content creation (writing, draft) → `multi-agent-framework/kumachii-content`
- Finance tracking/categorization → `multi-agent-framework/kumachii-finance`
- Forward task ke STELLA → `multi-agent-framework-shared/handoff-template` + `kumachii-handoff`
- Multi-agent project escalation → `autonomous-ai-agents/multi-agent-orchestration`
```

#### STELLA
```markdown
## Skills (MANDATORY)

Sebelum delegasi/gate, load skill:
- Setiap delegation ke specialist → `multi-agent-framework-shared/handoff-template`
- Gate transition (Gate 0-6) → `multi-agent-framework-shared/gate-checklist` + `multi-agent-framework/stella-gate-enforcer`
- Decision log entry → `multi-agent-framework/stella-decision-log`
- Routing keputusan ke specialist mana → `multi-agent-framework/stella-delegation-router`
- Workflow state Redis → `stella-redis-state`
- Multi-spec orchestration → `software-development/multi-spec-orchestration`
```

#### EDISON
```markdown
## Skills (MANDATORY)

Sebelum implement, load skill:
- TDD task baru → `multi-agent-framework-shared/tdd` + `software-development/test-driven-development`
- Atomic commit pattern → `multi-agent-framework/edison-atomic-commits`
- Story implementation → `multi-agent-framework/edison-story-impl`
- Debugging stuck → `software-development/systematic-debugging`
- Report ke STELLA → `multi-agent-framework-shared/handoff-template`
```

#### SHAKA
```markdown
## Skills (MANDATORY)

Sebelum design, load skill:
- Architecture diagram → `creative/architecture-diagram` atau `creative/excalidraw`
- Implementation plan → `software-development/writing-plans` + `software-development/plan`
- Report ke STELLA → `multi-agent-framework-shared/handoff-template`
```

#### ATLAS
```markdown
## Skills (MANDATORY)

Sebelum security review, load skill:
- Security review report → `multi-agent-framework-shared/security-review`
- Code review (security angle) → `github/github-code-review`
- Report ke STELLA → `multi-agent-framework-shared/handoff-template`
```

#### YORK
```markdown
## Skills (MANDATORY)

Sebelum QA review, load skill:
- QA review report → `multi-agent-framework-shared/qa-review`
- TDD verification → `multi-agent-framework-shared/tdd`
- Pre-commit review → `software-development/requesting-code-review`
- Security cross-check (max 20%) → `multi-agent-framework-shared/security-review`
- Report ke STELLA → `multi-agent-framework-shared/handoff-template`
```

#### LILITH
```markdown
## Skills (MANDATORY)

Sebelum deploy, load skill:
- Deployment checklist → `multi-agent-framework-shared/deployment-check`
- Webhook subscription → `devops/webhook-subscriptions`
- Health watchdog → `devops/health-watchdog-cronjob`
- Security pre-deploy → `multi-agent-framework-shared/security-review`
- Report ke STELLA → `multi-agent-framework-shared/handoff-template`
```

#### BONNEY
```markdown
## Skills (MANDATORY)

Sebelum doc/RAG, load skill:
- RAG pipeline ingestion → `multi-agent-framework-shared/rag-ingestion`
- Obsidian vault → `note-taking/obsidian`
- Notion docs → `productivity/notion`
- PDF/OCR → `productivity/ocr-and-documents` atau `productivity/nano-pdf`
- Report ke STELLA → `multi-agent-framework-shared/handoff-template`
```

#### PYTHAGORAS
```markdown
## Skills (MANDATORY)

Sebelum research, load skill:
- Research report → `multi-agent-framework/pythagoras-research-report`
- Comparison/benchmark → `multi-agent-framework/pythagoras-comparison`
- Academic paper search → `research/arxiv`
- Blog/RSS monitoring → `research/blogwatcher`
- Market data → `research/polymarket`
- LLM knowledge base → `research/llm-wiki`
- Report ke STELLA → `multi-agent-framework-shared/handoff-template`
```

#### STUSSY
```markdown
## Skills (MANDATORY)

Sebelum monitoring/ops, load skill:
- Health watchdog cronjob → `devops/health-watchdog-cronjob`
- Webhook subscription → `devops/webhook-subscriptions`
- Kanban worker (operasional task) → `devops/kanban-worker`
- Deployment verification → `multi-agent-framework-shared/deployment-check`
- Report ke STELLA → `multi-agent-framework-shared/handoff-template`
```

---

## Skill `9router` Universal Mandate (untuk semua profile)

Skill `multi-agent-framework/9router` saat ini **belum di-bundle** di profile lain (cek manifest 2026-05-17 — hanya ada di default profile). Kalau profile non-default akan call LLM/image/TTS via `127.0.0.1:20128`, mereka harus juga di-bundle skill `9router` plus mandate yang sama:

```markdown
**WAJIB load skill `9router` SEBELUM** menyentuh `http://127.0.0.1:20128` —
chat, image, tts, embedding, web-search, web-fetch, stt.
```

---

## Action Items (untuk Gilang approve)

1. **Patch SOUL.md per profile** dengan section "Skills (Mandatory)" sesuai role (10 patch).
2. **Bundle skill `multi-agent-framework/9router`** ke profile yang akan call gateway LLM (paling tidak: stella, edison, pythagoras, bonney, kumachii).
3. **Verify** dengan cara: spawn 1 agent (mis. EDISON) dengan task TDD → cek log apakah dia load `tdd` + `edison-atomic-commits` skill saat runtime.
4. **Optional**: tambah `skills.allowlist` di `config.yaml` per profile untuk batasi ke skill yang relevan saja (kurangi noise di skill picker).

---

## Pitfall Catatan

- **Skill ada di disk ≠ skill ke-load.** Hermes runtime hanya inject skill list metadata; agent harus eksplisit panggil `skill_view(name)` untuk dapat content.
- **`handoff-template` doang juga setengah-setengah** — di-mandate hanya saat *report*, bukan saat *delegate*. STELLA delegasi tanpa load `handoff-template` masih possible.
- **Skill spesifik per agent (mis. `stella-gate-enforcer`) yang sudah dibikin tapi ngga di-mandate = waste effort** kalau tidak di-surface lewat SOUL.md.

---

## Source Files Audited

```
/home/infra/.hermes/profiles/atlas/SOUL.md       (280 lines)
/home/infra/.hermes/profiles/bonney/SOUL.md      (239 lines)
/home/infra/.hermes/profiles/edison/SOUL.md      (208 lines)
/home/infra/.hermes/profiles/kumachii/SOUL.md    (75 lines)
/home/infra/.hermes/profiles/lilith/SOUL.md      (223 lines)
/home/infra/.hermes/profiles/pythagoras/SOUL.md  (238 lines)
/home/infra/.hermes/profiles/shaka/SOUL.md       (240 lines)
/home/infra/.hermes/profiles/stella/SOUL.md      (261 lines)
/home/infra/.hermes/profiles/stussy/SOUL.md      (251 lines)
/home/infra/.hermes/profiles/york/SOUL.md        (258 lines)
```

Skill bundle manifest: `~/.hermes/profiles/<name>/skills/.bundled_manifest`

---

**End of audit.**
