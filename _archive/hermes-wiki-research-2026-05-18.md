# Research: hermes-wiki + wondelai/skills — Adoption ke Multi-Agent Workflow

**Tanggal**: 2026-05-18
**Subjek**: Deep research dua repo, mapping ke framework Kumachii-Stella (11 profil)
**Sumber**:
1. `/home/infra/hermes-wiki/` — clone Hermes-Wiki resmi (Nous Research, v2026.4.23, 37 concept pages, line-by-line source-verified)
2. `https://github.com/wondelai/skills` — koleksi 42 agent-skills domain (product/UX/marketing/sales/code-quality/system-design)

**TL;DR**:
- **hermes-wiki** = arsitektur reference. **Wajib dipakai** sebagai source of truth untuk patch SOUL.md, design pattern internal (delegate_task budget, prompt-cache friendliness, security layers, curator).
- **wondelai/skills** = 42 skill konten (jobs-to-be-done, clean-code, system-design, ddia-systems, refactoring-ui, dst). Sebagian relevan langsung untuk profil Kumachii-Stella (EDISON, SHAKA, BONNEY, KUMACHII).
- Adopsi: **lift hermes-wiki design patterns** ke SOUL.md mandate kita; **install subset wondelai skills** ke profil yang cocok (10 dari 42 high-fit).

---

## Bagian 1 — Hermes-Wiki: Apa & Strukturnya

### 1.1 Identitas

- Repo: `https://github.com/cclank/Hermes-Wiki.git`
- Local: `/home/infra/hermes-wiki/`
- Domain: dokumentasi arsitektur Hermes Agent (open-source Nous Research)
- Tracked version: v2026.4.23 (last update 2026-04-29)
- Format: 37 concept pages + 2 entity pages + 5 changelog, total ~10.7k lines markdown
- Setiap page **line-by-line source-code verified** — bukan generated, bukan halu

### 1.2 Konvensi (SCHEMA.md)

- Filename: lowercase + hyphens
- Frontmatter YAML wajib (title, created, updated, type, tags, sources)
- Bidirectional wiki links (`[[page-name]]`), minimal 2 outbound per page
- Append-only `log.md` untuk audit trail
- Tag taxonomy fix: Architecture, Skills, Memory, Tools, Agent, Gateway, Performance, Security, dst
- Page threshold rule: split kalau >200 line, archive kalau superseded

### 1.3 Cakupan Konten (37 concepts)

| Kategori | Skills/Concept | Relevansi ke Multi-Agent Kita |
|---|---|---|
| **Core Architecture** | agent-loop-and-prompt-assembly, tool-registry-architecture, model-tools-dispatch, toolsets-system, prompt-builder-architecture, auxiliary-client-architecture, provider-transport-architecture | HIGH — basis 11 profile |
| **Memory & Sessions** | memory-system-architecture, session-search-and-sessiondb, context-compressor-architecture, skills-and-memory-interaction, skills-system-architecture | CRITICAL — gap sekarang di SOUL audit |
| **Tools & Capabilities** | browser-tool-architecture, web-tools-architecture, code-execution-sandbox, voice-mode-architecture, context-references, fuzzy-matching-engine, large-tool-result-handling | HIGH — pakai untuk EDISON/PYTHAGORAS |
| **Performance** | parallel-tool-execution, prompt-caching-optimization, smart-model-routing | CRITICAL — affect cost & speed |
| **Security & Reliability** | security-defense-system, interrupt-and-fault-tolerance, credential-pool-and-isolation | CRITICAL — affect ATLAS profile |
| **Multi-Agent** | multi-agent-architecture, configuration-and-profiles | DIRECT MATCH — ini fondasi kita |
| **Platform & Extensions** | cli-architecture, terminal-backends, messaging-gateway-architecture, gateway-session-management, hook-system-architecture, mcp-and-plugins, skin-engine, worktree-isolation, cron-scheduling, trajectory-and-data-generation | MEDIUM — ops & devops |

---

## Bagian 2 — Hermes-Wiki: Pattern Yang Wajib Diadopsi

### 2.1 Multi-Agent Architecture (DIRECT FIT)

Wiki halaman `multi-agent-architecture.md` (618 lines) adalah **blueprint** yang harus jadi fondasi profil STELLA + EDISON kita:

**Konstanta penting**:
```python
MAX_DEPTH = 2                # Parent(0) → Child(1) → Grandchild rejected
MAX_CONCURRENT_CHILDREN = 3  # Maksimal 3 child agent paralel
DEFAULT_MAX_ITERATIONS = 50  # Limit iterasi per child
DELEGATE_BLOCKED_TOOLS = {"delegate_task", "clarify", "memory", "send_message", "execute_code"}
```

**Insight kritis**:
1. `delegate_task` punya **role parameter** (`leaf` | `orchestrator`). Default flat (depth=1). Untuk STELLA jadi orchestrator, naikkan `max_spawn_depth=2-3`.
2. **Cross-agent file state coordination** (v2026.4.18+): file written by child A visible to child B + parent. Mencegah patch loss.
3. **Iteration budget isolation**: Parent 90 + 3 children × 50 = 240 total budget — tidak saling makan.
4. **Background Review** (counter-based): tiap N tool call, fork silent agent untuk extract experience → auto-create skill. Ini mekanisme self-evolution kita.

**Yang perlu kita adopsi**:
- Atur `delegation.max_concurrent_children=3` di config STELLA
- Atur `delegation.max_spawn_depth=2` (STELLA → EDISON → leaf-of-EDISON)
- STELLA pakai `role=orchestrator`, EDISON/atlas/dll pakai `role=leaf`
- Set `skills.creation_nudge_interval=15` di profil aktif untuk auto-skill creation

### 2.2 Skills System: Progressive Disclosure (sudah dimiliki, perlu di-enforce)

Wiki halaman `skills-system-architecture.md`:
- **Sistem `requires_tools` / `fallback_for_toolsets`** untuk conditional activation. Skills hanya muncul kalau tool yang dibutuhkan tersedia.
- **Curator** (v2026.4.23+): background maintenance, archive idle skills (default 7 days unused → stale → archived). Dipanggil otomatis di gateway startup.
- **Pinned skills**: `_pinned_guard()` blokir modifikasi. Cocok untuk skill load-bearing kita (`9router`, `handoff-template`, `tdd`).

**Yang perlu kita adopsi**:
- Pin skill kritis per role (e.g., `9router` di profile yang akses LLM, `handoff-template` di semua, `tdd` di EDISON)
- Aktifkan curator: `hermes curator status` → check baseline, lalu `pin` skill load-bearing
- Tambah `requires_toolsets` di SKILL.md skill role-specific (`stella-gate-enforcer` → requires_toolsets: [delegation])

### 2.3 Skills + Memory Interaction (gap di SOUL audit kita)

Wiki halaman `skills-and-memory-interaction.md`:
- **Memory** = facts (preference, environment, quirk). 2200 char MEMORY.md, 1375 char USER.md.
- **Skills** = procedural (workflow, command sequence). Tidak ada hard limit.
- **Decision tree**: simple stable fact → Memory; complex 5+ tool process → Skill.

Audit kita 2026-05-18 menunjukkan: 9/10 profile cuma sebut `handoff-template` 1× di SOUL. **Tidak ada section "Skills (Mandatory)"** dengan trigger condition. Wiki ini provide template-nya.

### 2.4 Configuration & Multi-Profile

Wiki halaman `configuration-and-profiles.md`:
- Setiap Profile = `HERMES_HOME` directory penuh (config, memory, skills, gateway, db).
- 119+ file di codebase resolve via `get_hermes_home()`.
- Profile activation = `_apply_profile_override()` di module level (sebelum `import`).
- **Built-in skills sync via subprocess** (bukan in-process), karena `sync_skills()` cache HERMES_HOME at module level. Penting untuk `hermes update` di multi-profile setup kita.

**Yang perlu kita adopsi**:
- Audit semua 11 profile pakai `hermes profile list` (verifikasi state)
- Per profile config terminal backend yang sesuai role:
  - KUMACHII / PYTHAGORAS / BONNEY / ATLAS → `terminal: disabled` (sudah, lewat toolset block)
  - EDISON → `terminal: local`
  - LILITH (DevOps) → `terminal: local` + cronjob
  - STELLA → `terminal: local` (inspect-only mode, harus delegate destructive ke EDISON)

### 2.5 Security Defense (5-layer) — kritis untuk ATLAS

Wiki halaman `security-defense-system.md`:
- 100+ regex threat patterns (data exfiltration, prompt injection, destructive ops, persistent backdoor, network backdoor, supply chain)
- Trust level policy: `builtin/trusted/community/agent-created` × `safe/caution/dangerous`
- 17 invisible Unicode chars detected (zero-width space, RTL override, BOM, dll)
- Approval modes: `manual / smart / off`. Default `smart` pakai aux LLM untuk risk assessment.
- Per-session approval state via `contextvars.ContextVar`.

**Yang perlu kita adopsi**:
- Set `approvals.mode=smart` di profile destructive (LILITH, EDISON)
- Set `approvals.mode=off` HANYA di KUMACHII/BONNEY (no terminal anyway)
- ATLAS profile baca section ini sebagai SOUL primer — ini scope-nya

### 2.6 Parallel Tool Execution

Wiki halaman `parallel-tool-execution.md`:
- Hermes auto-paralelkan tool call kalau aman:
  - Read-only safe set: `read_file, search_files, web_search, web_extract, vision_analyze, skill_view, skills_list, session_search`
  - Path-scoped: `read_file, write_file, patch` — paralel kalau path tidak overlap
  - Never-parallel: `clarify`
- Path conflict detection via prefix-match
- Max 8 worker threads
- Default **conservative downgrade ke serial** kalau ada uncertainty

**Yang perlu kita adopsi**:
- Update SOUL Bagian 3 (Parallel Tool Calling) — referensikan list ini supaya akurat
- Tambah note: "Hermes auto-detect path conflict di file tools, tidak perlu manual sequential"

### 2.7 Credential Pool & Isolation

Wiki halaman `credential-pool-and-isolation.md`:
- 4 strategi: `fill_first`, `round_robin`, `random`, `least_used`
- Auto-rotation pada 401/402/429
- Exhaustion TTL 1 jam, auto-recovery
- OAuth refresh built-in

**Yang perlu kita adopsi**:
- Tidak urgent karena 9router sudah handle pool di gateway level. Tapi worth dokumentasi di skill `9router` untuk reference.

---

## Bagian 3 — wondelai/skills: Apa & Strukturnya

### 3.1 Identitas

- Repo: `https://github.com/wondelai/skills`
- 998 stars, 114 forks, 48 commits, 1 branch (per fetch 2026-05-18)
- Format: agentskills.io-compatible (Claude Code plugin marketplace)
- 42 skills total, distribusi domain:
  - Product/Strategy (8): jobs-to-be-done, lean-startup, design-sprint, crossing-the-chasm, blue-ocean-strategy, traction-eos, inspired-product, 37signals-way
  - UX/Design (10): refactoring-ui, ios-hig-design, design-everyday-things, ux-heuristics, web-typography, top-design, hooked-ux, lean-ux, microinteractions, continuous-discovery
  - Marketing/Sales (9): cro-methodology, scorecard-marketing, storybrand-messaging, influence-psychology, predictable-revenue, made-to-stick, contagious, one-page-marketing, hundred-million-offers
  - Business/Negotiation (4): negotiation, mom-test, obviously-awesome, drive-motivation
  - Software Engineering (10): clean-code, refactoring-patterns, software-design-philosophy, pragmatic-programmer, domain-driven-design, ddia-systems, system-design, clean-architecture, release-it, high-perf-browser
  - Other (1): improve-retention

### 3.2 Format SKILL.md (compatible dengan Hermes)

```yaml
---
name: skill-name                              # max 64 char
description: 'Framework based on Author "Book". Use when: (1) X, (2) Y, (3) Z'  # max 1024
license: MIT
metadata:
  author: wondelai
  version: "1.0.0"
---

# Skill Title
## Core Principle
## Scoring (0-10)
## Framework Sections (numbered)
## Common Mistakes (table)
## Quick Diagnostic (table)
## Reference Files
```

Format identik dengan SKILL.md schema Hermes (lihat wiki `skills-system-architecture.md`). Bisa di-install langsung lewat `git clone` ke `~/.hermes/skills/<name>/`.

### 3.3 Sample Quality

Tiap skill punya:
- Core principle 1 paragraf (north-star)
- Scoring rubric 0-10 untuk self-evaluation
- Framework sections bernomor: konsep, why-it-works, key insights, applications table, copy patterns, ethical boundary, references
- Common mistakes table (Mistake | Why Fails | Fix)
- Quick diagnostic table (Question | If No | Action)

Format ini **closer to Anthropic's Claude Skills** ketimbang random README — structure konsisten.

---

## Bagian 4 — Mapping wondelai → Profil Kumachii-Stella

### 4.1 High-Fit (recommend install)

| Skill | Cocok ke profil | Rasional |
|---|---|---|
| **clean-code** | EDISON | TDD/atomic commits sudah ada, tambah readable code principle (Robert C. Martin) |
| **refactoring-patterns** | EDISON | Named refactoring transformations (Martin Fowler) — referensi saat refactor task |
| **software-design-philosophy** | EDISON, SHAKA | Managing complexity (Ousterhout) — fondasi architectural decision |
| **clean-architecture** | SHAKA | Dependency Rule + layer separation — directly relevant untuk SHAKA architect role |
| **system-design** | SHAKA | Scalable distributed systems — base untuk SHAKA propose architecture |
| **ddia-systems** | SHAKA, EDISON | Designing Data-Intensive Applications — for any DB/queue/replication design |
| **release-it** | LILITH | Production-ready systems (Nygard) — circuit breaker, bulkhead, timeouts |
| **pragmatic-programmer** | EDISON, KUMACHII | Meta-principle craftsmanship — broad applicability |
| **domain-driven-design** | SHAKA | Modeling around business domain — useful saat scoping new feature |
| **mom-test** | KUMACHII, BONNEY | Customer interview framework — non-leading questions saat user research |

### 4.2 Medium-Fit (install kalau Gilang start product/UX project)

| Skill | Cocok ke | Rasional |
|---|---|---|
| **jobs-to-be-done** | KUMACHII (PA) | Untuk help Gilang plan product features atau campus project |
| **lean-startup** | KUMACHII | Build-Measure-Learn untuk experiment-driven Gilang |
| **refactoring-ui** | EDISON, KUMACHII | UI design cepat (Tailwind context relevant) |
| **continuous-discovery** | KUMACHII | Weekly customer touchpoints — workflow planning |
| **negotiation** | KUMACHII | Tactical empathy (Chris Voss) — useful Gilang freelance |
| **storybrand-messaging** | BONNEY | Doc/RAG profile — story-based messaging untuk technical writing |

### 4.3 Skip (low-fit untuk engineering-heavy framework)

- contagious, made-to-stick, scorecard-marketing, hundred-million-offers, one-page-marketing, predictable-revenue, cro-methodology, influence-psychology — pure marketing/sales, tidak fit Gilang's engineering work
- ios-hig-design, web-typography, top-design, microinteractions, hooked-ux, design-everyday-things, ux-heuristics, lean-ux — UX design, hanya install kalau ada UI project aktif
- design-sprint, crossing-the-chasm, blue-ocean-strategy, traction-eos, inspired-product, 37signals-way, obviously-awesome — bisnis strategy, optional

### 4.4 Total Adoption Recommendation

**Phase 1 (immediate, 10 skills)** — install ke `~/.hermes/skills/` lalu pin per profile via curator:

```bash
cd ~/.hermes/skills/
git clone https://github.com/wondelai/skills.git wondelai-skills-source
# Copy hanya 10 skill terpilih ke direktori top-level
for s in clean-code refactoring-patterns software-design-philosophy clean-architecture \
         system-design ddia-systems release-it pragmatic-programmer \
         domain-driven-design mom-test; do
  cp -r wondelai-skills-source/$s ./
done
rm -rf wondelai-skills-source
```

Lalu update profile config — pakai `skills.config.allowed` per profile untuk filter:
- EDISON: clean-code, refactoring-patterns, software-design-philosophy, pragmatic-programmer, domain-driven-design
- SHAKA: clean-architecture, system-design, ddia-systems, software-design-philosophy, domain-driven-design
- LILITH: release-it
- KUMACHII: pragmatic-programmer, mom-test
- BONNEY: pragmatic-programmer

**Phase 2 (kalau ada need)** — install medium-fit on demand.

---

## Bagian 5 — Action Plan untuk Multi-Agent Workflow

### 5.1 Immediate (high-leverage)

1. **Patch SOUL.md tiap profile** dengan section "## Skills (Mandatory)" — sudah didokumentasi di `agent-skills-mandate-audit-2026-05-18.md`. Pakai hermes-wiki sebagai authoritative reference untuk wording.

2. **Pin load-bearing skills** per profile via curator:
   ```
   hermes -p stella curator pin gate-checklist
   hermes -p stella curator pin handoff-template
   hermes -p edison curator pin tdd
   hermes -p edison curator pin test-driven-development
   hermes -p atlas curator pin grill-me
   ```

3. **Setup delegation config di STELLA**:
   ```yaml
   # ~/.hermes/profiles/stella/config.yaml
   delegation:
     max_concurrent_children: 3
     max_spawn_depth: 2
     orchestrator_enabled: true
   ```

4. **Install 10 wondelai skill high-fit** (lihat 4.4) dan pin per role.

### 5.2 Medium (next sprint)

5. **Adopt Curator state machine** di semua profile (default 7 days idle → archive). Stella + EDISON skill volume tinggi, ini akan otomatis cleanup.

6. **Tambah `requires_toolsets` di SKILL.md** semua role-specific skill (e.g., `stella-gate-enforcer` → `requires_toolsets: [delegation]`). Ini buat skill auto-disappear di profile yang tidak punya toolset.

7. **Audit security pattern** di skill kustom kita pakai Skills Guard 100+ pattern reference dari wiki. Run `hermes skills lint` (atau equivalent).

### 5.3 Long-term (architectural)

8. **Mirror konvensi Hermes-Wiki untuk knowledge base kita** — `~/.hermes/knowledge/topics/` jadi mini-wiki dengan SCHEMA.md, log.md, index.md, frontmatter, bidirectional links. Background curator agent (cron) bisa maintain.

9. **Implement Background Review** — Hermes built-in mekanisme `_iters_since_skill` counter. Aktifkan di profile EDISON & SHAKA (heavy tool-call profiles). Akan auto-extract patterns ke skills baru.

10. **Cross-Profile communication via Discord** (sudah diimplement) — wiki halaman `multi-agent-architecture.md` section IV explicitly state ini "not Hermes-designed" dan rawan loop. Add safety: rate limit per bot, dedup pesan, kill switch via env var.

---

## Bagian 6 — Risk & Pitfall

| Risk | Mitigasi |
|---|---|
| wondelai skill collision dengan built-in nama | Install di subdirectory `wondelai/`, atau rename prefix `w-clean-code` |
| Curator archive skill kustom yang masih relevant | **Pin** semua role-specific skill kita (`stella-*`, `edison-*`) sebelum activate curator |
| `delegation.max_spawn_depth=2` bikin grandchild leak | Test di staging dulu, monitor `_active_children` count |
| Skills Guard block wondelai skill (false positive) | Wondelai pure educational text, low risk. Kalau hit pattern, edit description-nya bukan disable scanner |
| Hermes-Wiki out of date vs runtime kita | Wiki tracks v2026.4.23, kita pakai versi mana? Run `hermes --version` untuk verifikasi sebelum apply pattern |
| Multi-bot Discord loop (lihat 5.3 #10) | Sudah ada `DISCORD_ALLOW_BOTS=all` per profile. Tambah `Hermes-Agent` mention filter di gateway hook |

---

## Bagian 7 — Source References

- `/home/infra/hermes-wiki/concepts/multi-agent-architecture.md` (618 lines)
- `/home/infra/hermes-wiki/concepts/skills-system-architecture.md` (317 lines)
- `/home/infra/hermes-wiki/concepts/configuration-and-profiles.md` (291 lines)
- `/home/infra/hermes-wiki/concepts/security-defense-system.md` (397 lines)
- `/home/infra/hermes-wiki/concepts/parallel-tool-execution.md` (194 lines)
- `/home/infra/hermes-wiki/concepts/credential-pool-and-isolation.md` (171 lines)
- `/home/infra/hermes-wiki/concepts/hook-system-architecture.md` (402 lines)
- `/home/infra/hermes-wiki/concepts/cron-scheduling.md` (211 lines)
- `/home/infra/hermes-wiki/concepts/skills-and-memory-interaction.md` (151 lines)
- `https://github.com/wondelai/skills` (README.md, CLAUDE.md)
- `~/.hermes/knowledge/topics/agent-skills-mandate-audit-2026-05-18.md` (audit gap kita)

