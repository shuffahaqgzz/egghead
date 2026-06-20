# Kumachii-Stella Skills Architecture Plan

**Tanggal**: 2026-05-21
**Status**: Draft — awaiting Gilang review
**Scope**: 11 profiles, 123 global skills, 26 categories

---

## Problem Statement

Semua 10 profile specialist (kecuali default) punya **salinan lokal ~79 skill boilerplate** yang identik dari global `~/.hermes/skills/`. Ketika skill baru ditambahkan ke global (contoh: `9router`, `hermes-wiki`, role-specific skills), **tidak ada propagasi otomatis** ke profile.

Akibat:
- `9router` (LLM gateway) — TIDAK ada di manapun kecuali default
- `hermes-wiki` (architecture reference) — TIDAK ada di manapun kecuali default
- Role-specific skills (`atlas-*`, `bonney-*`, `edison-*`, `lilith-*`, `stussy-*`, `york-*`, `shaka-*`, `pythagoras-*`) — TIDAK ada di profile masing-masing
- `honcho-self-host` (memory backend) — TIDAK ada di profile

Selain itu, ada **directory-level override** yang bikin kumachii's local `multi-agent-framework/` (4 skills) block visibility ke global `multi-agent-framework/` (13 skills).

---

## Bagian 1 — Propagate Global Skills ke Semua Profile

### 1.1 Current State

| Profile | Local Skills | Missing from Global |
|---|---|---|
| default (hermes) | 123 (global) | n/a |
| kumachii | 90 (79 boilerplate + 11 role-specific) | 9router, hermes-wiki, honcho-self-host, stella-*, edison-*, pythagoras-*, atlas-*, bonney-*, lilith-*, york-*, shaka-*, stussy-* |
| stella | 92 (79 boilerplate + 13 role-specific) | 9router, hermes-wiki, honcho-self-host, atlas-*, bonney-*, edison-*, lilith-*, york-*, shaka-*, stussy-*, pythagoras-* |
| edison | 79 (all boilerplate) | 9router, hermes-wiki, honcho-self-host, ALL role-specific |
| atlas | 79 (all boilerplate) | 9router, hermes-wiki, ALL role-specific |
| bonney | 79 (all boilerplate) | 9router, hermes-wiki, ALL role-specific |
| lilith | 79 (all boilerplate) | 9router, hermes-wiki, ALL role-specific |
| pythagoras | 79 (all boilerplate) | 9router, hermes-wiki, ALL role-specific |
| shaka | 80 (79 boilerplate + 1) | 9router, hermes-wiki, ALL role-specific |
| stussy | 80 (79 boilerplate + 1) | 9router, hermes-wiki, ALL role-specific |
| york | 80 (79 boilerplate + 1) | 9router, hermes-wiki, ALL role-specific |

### 1.2 Propagation Strategy

**2 opsi (Gilang pilih):**

#### Opsi A: Symlink (recommended)

Buat symlink dari profile local dir → global skill dir. Skill auto-sync saat global di-update.

```bash
# Per profile, symlink missing skills
PROFILE=edison
PROFILE_SKILLS=~/.hermes/profiles/$PROFILE/skills

# Symlink shared framework skills (SEMUA profile)
for skill in 9router hermes-wiki honcho-self-host; do
  ln -sf ~/.hermes/skills/multi-agent-framework/$skill \
         $PROFILE_SKILLS/multi-agent-framework/$skill
done

# Symlink role-specific skills ke profile yang sesuai
ln -sf ~/.hermes/skills/software-development/shaka-architecture \
       ~/.hermes/profiles/shaka/skills/software-development/shaka-architecture
# ...dst
```

**Pro**: Auto-sync, no duplication, minimal disk usage
**Con**: Symlink bisa break kalau global reorganized, perlu `requires_toolsets` di SKILL.md untuk isolation

#### Opsi B: `skills.external_dirs` di config

Tambah global skills dir ke setiap profile config:

```yaml
# ~/.hermes/profiles/<name>/config.yaml
skills:
  external_dirs:
    - /home/infra/.hermes/skills
```

**Pro**: Clean config, no symlinks, merge discovery
**Con**: SEMUA profile lihat SEMUA 123 skills (no role isolation), perlu `skills.disabled` untuk filter

### 1.3 Shared Skills yang HARUS ada di SEMUA profile

| Skill | Category | Purpose |
|---|---|---|
| `9router` | multi-agent-framework | LLM gateway access — semua agent perlu route ke AI |
| `hermes-wiki` | multi-agent-framework | Architecture reference — semua agent perlu tau pattern |
| `honcho-self-host` | multi-agent-framework | Memory backend — semua agent pakai Honcho |
| `handoff-template` | multi-agent-framework-shared | Inter-agent handoff format |
| `grill-me` | multi-agent-framework-shared | Self-review challenge pattern |
| `gate-checklist` | multi-agent-framework-shared | Phase gate 0-6 checklist |

### 1.4 Role-Specific Skills yang perlu di-propagate ke profile tertentu

| Skill | Category | Target Profile(s) |
|---|---|---|
| `stella-orchestration` | multi-agent-framework | stella |
| `stella-delegation-router` | multi-agent-framework | stella |
| `stella-gate-enforcer` | multi-agent-framework | stella |
| `stella-decision-log` | multi-agent-framework | stella |
| `stella-redis-state` | multi-agent-framework | stella |
| `edison-atomic-commits` | multi-agent-framework | edison |
| `edison-story-impl` | multi-agent-framework | edison |
| `pythagoras-comparison` | multi-agent-framework | pythagoras |
| `pythagoras-research-report` | multi-agent-framework | pythagoras |
| `atlas-block-release` | security | atlas |
| `atlas-security-review` | security | atlas |
| `atlas-threat-model` | security | atlas |
| `bonney-docs-standards` | documentation | bonney |
| `bonney-rag-pipeline` | documentation | bonney |
| `bonney-source-hierarchy` | documentation | bonney |
| `lilith-commissioning` | devops | lilith |
| `lilith-deployment` | devops | lilith |
| `lilith-rollback` | devops | lilith |
| `york-acceptance-validation` | qa | york |
| `york-block-release` | qa | york |
| `york-review-checklist` | qa | york |
| `stussy-block-release` | ops | stussy |
| `stussy-health-check` | ops | stussy |
| `stussy-incident-response` | ops | stussy |
| `stussy-runbook` | ops | stussy |
| `shaka-adr` | software-development | shaka |
| `shaka-architecture` | software-development | shaka |
| `shaka-readiness-check` | software-development | shaka |

---

## Bagian 2 — Skills Mapping per Agent Specialist

### 2.1 KUMACHII — Personal Assistant & Intake

**Role**: Intake, content, finance, schedule, campus support
**SOUL.md mandate**: Office work, content creation, campus education, finance, personal planning

| Type | Skills |
|---|---|
| Role-specific (local) | kumachii-handoff, kumachii-intake, kumachii-content, kumachii-finance |
| Shared framework | handoff-template, grill-me, gate-checklist |
| Gateway | 9router |
| Reference | hermes-wiki |
| Memory | honcho-self-host |
| Email | himalaya |
| Notes | obsidian |
| Productivity | google-workspace, notion, airtable, linear, maps, nano-pdf, ocr-and-documents, powerpoint, teams-meeting-pipeline |
| Content | humanizer, creative-ideation, gif-search, spotify, youtube-content |
| Research (campus) | arxiv, blogwatcher, llm-wiki, research-paper-writing |
| Social | xurl |
| Personal | load-context, session-handoff |
| MCP | native-mcp |
| Wondelai (pending) | pragmatic-programmer, mom-test |

**DISABLE**: apple-*, claude-code, codex, opencode, hermes-agent, architecture-diagram, baoyu-*, claude-design, comfyui, design-md, excalidraw, manim-video, p5js, pixel-art, popular-web-designs, pretext, sketch, songwriting-and-ai-music, touchdesigner-mcp, ascii-art, ascii-video, kanban-*, webhook-subscriptions, jupyter-live-kernel, huggingface-hub, godmode, openhue, dogfood, minecraft-modpack-server, pokemon-player, polymarket, debugging-*, hermes-agent-skill-authoring, node-inspect-debugger, plan, python-debugpy, requesting-code-review, spike, subagent-driven-development, systematic-debugging, test-driven-development, writing-plans, multi-spec-orchestration, disk-storage-audit, codebase-inspection, github-*

### 2.2 STELLA — Orchestrator

**Role**: Multi-agent routing, gate approval, delegation, decision logging
**SOUL.md mandate**: Plan complex work, route to right agent, track progress, enforce gates

| Type | Skills |
|---|---|
| Role-specific (local) | stella-orchestration, stella-delegation-router, stella-gate-enforcer, stella-decision-log, stella-redis-state, edison-tdd-pipeline, gate-templates |
| Shared framework | handoff-template, grill-me, gate-checklist, deployment-check, qa-review |
| Gateway | 9router |
| Reference | hermes-wiki |
| Memory | honcho-self-host |
| Multi-agent | multi-agent-orchestration, multi-spec-orchestration |
| MCP | native-mcp |

**DISABLE**: apple-*, coding agents (except hermes-agent for config), creative-*, data-science, devops, email, gaming, github (except github-code-review for PR oversight), media-*, mlops, note-taking, productivity, red-teaming, research, smart-home, social-media, most software-development

### 2.3 EDISON — Coding TDD Agent

**Role**: Full code implementation, TDD, atomic commits, debugging
**SOUL.md mandate**: Implement code, run tests, refactor, commit atomically

| Type | Skills |
|---|---|
| Role-specific (local) | edison-atomic-commits, edison-story-impl |
| Shared framework | handoff-template, grill-me, gate-checklist, qa-review, rag-ingestion |
| Gateway | 9router |
| Reference | hermes-wiki |
| Memory | honcho-self-host |
| GitHub | github-auth, github-code-review, github-issues, github-pr-workflow, github-repo-management, codebase-inspection |
| Software dev | test-driven-development, systematic-debugging, requesting-code-review, writing-plans, plan, spike, subagent-driven-development, python-debugpy, node-inspect-debugger, debugging-hermes-tui-commands, hermes-agent-skill-authoring |
| Coding agents | claude-code, codex, opencode, hermes-agent |
| MCP | native-mcp |
| Wondelai (pending) | clean-code, refactoring-patterns, software-design-philosophy, pragmatic-programmer, domain-driven-design |

**DISABLE**: apple-*, creative-*, data-science, email, gaming, media-*, mlops, note-taking, productivity, red-teaming, research, smart-home, social-media, ops, qa (standalone), security (standalone), documentation

### 2.4 PYTHAGORAS — Research Agent

**Role**: Academic research, paper analysis, comparison reports
**SOUL.md mandate**: Literature review, data analysis, research reports

| Type | Skills |
|---|---|
| Role-specific (local) | pythagoras-comparison, pythagoras-research-report |
| Shared framework | handoff-template, grill-me, gate-checklist |
| Gateway | 9router |
| Reference | hermes-wiki |
| Memory | honcho-self-host |
| Research | arxiv, blogwatcher, llm-wiki, research-paper-writing, polymarket |
| MCP | native-mcp |

**DISABLE**: apple-*, coding agents, creative-*, data-science (unless jupyter needed), devops, email, gaming, github, media-*, mlops, note-taking, productivity, red-teaming, smart-home, social-media, software-development, ops, qa, security

### 2.5 ATLAS — Security Advisor

**Role**: Security review, threat modeling, block-release authority
**SOUL.md mandate**: Security audit, threat analysis, approve/block releases

| Type | Skills |
|---|---|
| Role-specific (local) | atlas-block-release, atlas-security-review, atlas-threat-model |
| Shared framework | handoff-template, grill-me, gate-checklist, security-review, qa-review |
| Gateway | 9router |
| Reference | hermes-wiki |
| Memory | honcho-self-host |
| Red-teaming | godmode |
| MCP | native-mcp |

**DISABLE**: apple-*, coding agents, creative-*, data-science, devops, email, gaming, github, media-*, mlops, note-taking, productivity, research, smart-home, social-media, software-development, ops, qa (standalone), documentation

### 2.6 YORK — QA Reviewer

**Role**: Code quality review, acceptance validation, test coverage, block-release authority
**SOUL.md mandate**: Review code, validate acceptance criteria, enforce quality gates

| Type | Skills |
|---|---|
| Role-specific (local) | york-acceptance-validation, york-block-release, york-review-checklist |
| Shared framework | handoff-template, grill-me, gate-checklist, qa-review, deployment-check |
| Gateway | 9router |
| Reference | hermes-wiki |
| Memory | honcho-self-host |
| GitHub | github-code-review, github-issues |
| Software dev | requesting-code-review |
| MCP | native-mcp |
| Dogfood | dogfood |

**DISABLE**: apple-*, coding agents (except hermes-agent), creative-*, data-science, devops, email, gaming, github (except code-review, issues), media-*, mlops, note-taking, productivity, red-teaming, research, smart-home, social-media, most software-development

### 2.7 LILITH — DevOps Agent

**Role**: Infrastructure, deployment, rollback, cron, monitoring setup
**SOUL.md mandate**: Deploy, configure infra, manage services, rollback

| Type | Skills |
|---|---|
| Role-specific (local) | lilith-commissioning, lilith-deployment, lilith-rollback |
| Shared framework | handoff-template, grill-me, gate-checklist, deployment-check |
| Gateway | 9router |
| Reference | hermes-wiki |
| Memory | honcho-self-host |
| DevOps | disk-storage-audit, kanban-orchestrator, kanban-worker, webhook-subscriptions |
| GitHub | github-auth, github-pr-workflow, github-repo-management |
| MCP | native-mcp |
| Wondelai (pending) | release-it |

**DISABLE**: apple-*, coding agents, creative-*, data-science, email, gaming, github (except auth/pr/repo), media-*, mlops, note-taking, productivity, red-teaming, research, smart-home, social-media, software-development (except plan/writing-plans), ops (standalone), qa (standalone), security, documentation

### 2.8 BONNEY — Documentation & RAG

**Role**: Documentation, RAG pipeline, source hierarchy management
**SOUL.md mandate**: Write docs, manage knowledge base, RAG ingestion

| Type | Skills |
|---|---|
| Role-specific (local) | bonney-docs-standards, bonney-rag-pipeline, bonney-source-hierarchy |
| Shared framework | handoff-template, grill-me, gate-checklist, rag-ingestion |
| Gateway | 9router |
| Reference | hermes-wiki |
| Memory | honcho-self-host |
| Productivity | google-workspace, notion, nano-pdf, ocr-and-documents, powerpoint |
| Research | research-paper-writing |
| Notes | obsidian |
| MCP | native-mcp |
| Wondelai (pending) | pragmatic-programmer, mom-test |

**DISABLE**: apple-*, coding agents, creative-*, data-science, devops, email, gaming, github, media-*, mlops, red-teaming, smart-home, social-media, software-development, ops, qa, security

### 2.9 STUSSY — Monitoring & Ops

**Role**: System monitoring, incident response, health checks, runbooks
**SOUL.md mandate**: Monitor systems, detect incidents, execute runbooks

| Type | Skills |
|---|---|
| Role-specific (local) | stussy-block-release, stussy-health-check, stussy-incident-response, stussy-runbook |
| Shared framework | handoff-template, grill-me, gate-checklist, deployment-check |
| Gateway | 9router |
| Reference | hermes-wiki |
| Memory | honcho-self-host |
| DevOps | disk-storage-audit |
| MCP | native-mcp |

**DISABLE**: apple-*, coding agents, creative-*, data-science, email, gaming, github, media-*, mlops, note-taking, productivity, red-teaming, research, smart-home, social-media, software-development, qa (standalone), security, documentation

### 2.10 SHAKA — Architect

**Role**: System architecture, ADR, design patterns, readiness review
**SOUL.md mandate**: Design architecture, write ADRs, review system design

| Type | Skills |
|---|---|
| Role-specific (local) | shaka-adr, shaka-architecture, shaka-readiness-check |
| Shared framework | handoff-template, grill-me, gate-checklist |
| Gateway | 9router |
| Reference | hermes-wiki |
| Memory | honcho-self-host |
| GitHub | github-code-review, codebase-inspection |
| Software dev | requesting-code-review, writing-plans, plan |
| MCP | native-mcp |
| Wondelai (pending) | clean-architecture, system-design, ddia-systems, software-design-philosophy, domain-driven-design |

**DISABLE**: apple-*, coding agents (except hermes-agent), creative-*, data-science, devops, email, gaming, github (except code-review/inspection), media-*, mlops, note-taking, productivity, red-teaming, research, smart-home, social-media, most software-development, ops, qa, security (standalone)

---

## Bagian 3 — Plan Revisi SOUL.md Mandatory Skills Section

### 3.1 Template Section

```markdown
---

## Skills (Mandatory)

Load skills **before** starting work. Follow trigger → load → execute.

| Skill | Trigger | When |
|---|---|---|
| 9router | Any LLM routing, model selection, provider test | Always (first tool) |
| hermes-wiki | Architecture question, "how does Hermes do X" | Before design decisions |
| handoff-template | Handing off work to another agent | Every handoff |
| grill-me | Self-review before declaring done | Every non-trivial task |
| [role-specific] | [role-specific trigger] | [role-specific condition] |

**Pin these skills** — they must not be archived by curator.
```

### 3.2 Per-Profile Mandatory Skills Draft

| Profile | Mandatory Skills (draft) |
|---|---|
| **kumachii** | 9router, hermes-wiki, handoff-template, grill-me, kumachii-intake, kumachii-handoff, kumachii-content, kumachii-finance |
| **stella** | 9router, hermes-wiki, handoff-template, grill-me, gate-checklist, stella-orchestration, stella-gate-enforcer, stella-delegation-router, stella-decision-log |
| **edison** | 9router, hermes-wiki, handoff-template, grill-me, test-driven-development, edison-atomic-commits, github-pr-workflow |
| **pythagoras** | 9router, hermes-wiki, handoff-template, grill-me, pythagoras-research-report, arxiv |
| **atlas** | 9router, hermes-wiki, handoff-template, grill-me, atlas-security-review, atlas-threat-model, atlas-block-release, security-review |
| **york** | 9router, hermes-wiki, handoff-template, grill-me, york-review-checklist, york-acceptance-validation, york-block-release, qa-review |
| **lilith** | 9router, hermes-wiki, handoff-template, grill-me, lilith-deployment, lilith-commissioning, lilith-rollback, deployment-check |
| **bonney** | 9router, hermes-wiki, handoff-template, grill-me, bonney-docs-standards, bonney-rag-pipeline, rag-ingestion |
| **stussy** | 9router, hermes-wiki, handoff-template, grill-me, stussy-health-check, stussy-incident-response, stussy-runbook |
| **shaka** | 9router, hermes-wiki, handoff-template, grill-me, shaka-architecture, shaka-adr, shaka-readiness-check |

### 3.3 Implementation Plan

1. Baca SOUL.md setiap profile
2. Append `## Skills (Mandatory)` section (template di 3.1)
3. Isi table per profile (mapping di 3.2)
4. Tambah note: `Pin these skills — they must not be archived by curator`
5. Test: `hermes -p <profile> chat -q "list mandatory skills"` — verify response matches

---

## Bagian 4 — Wondelai Adaptation Skills

### 4.1 High-Fit Skills (install sekarang)

| Skill | Source Book/Author | Target Profile(s) | Rationale |
|---|---|---|---|
| `clean-code` | Robert C. Martin | EDISON | Readable code principle, atomic functions, naming conventions. Complements TDD. |
| `refactoring-patterns` | Martin Fowler | EDISON | Named refactoring transformations. Reference saat EDISON refactor task. |
| `software-design-philosophy` | John Ousterhout | EDISON, SHAKA | Managing complexity, deep vs shallow modules. Architectural decision making. |
| `clean-architecture` | Robert C. Martin | SHAKA | Dependency Rule, layer separation. Directly relevant untuk SHAKA architect role. |
| `system-design` | Alex Xu | SHAKA | Scalable distributed systems. Base untuk SHAKA propose architecture. |
| `ddia-systems` | Martin Kleppmann | SHAKA, EDISON | Designing Data-Intensive Applications. DB/queue/replication design. |
| `release-it` | Michael Nygard | LILITH | Production-ready systems — circuit breaker, bulkhead, timeouts. DevOps core. |
| `pragmatic-programmer` | Hunt & Thomas | EDISON, KUMACHII, BONNEY | Meta-principle craftsmanship. Broad applicability. |
| `domain-driven-design` | Eric Evans | SHAKA, EDISON | Modeling around business domain. Useful saat scoping new feature. |
| `mom-test` | Rob Fitzpatrick | KUMACHII, BONNEY | Customer interview framework. Non-leading questions saat user research. |

### 4.2 Install Procedure

```bash
# 1. Clone wondelai repo ke temp dir
cd /tmp && git clone https://github.com/wondelai/skills.git wondelai-skills

# 2. Copy 10 high-fit skills ke global skills dir
SKILLS_DIR=~/.hermes/skills/wondelai
mkdir -p $SKILLS_DIR

for skill in clean-code refactoring-patterns software-design-philosophy \
             clean-architecture system-design ddia-systems release-it \
             pragmatic-programmer domain-driven-design mom-test; do
  cp -r /tmp/wondelai-skills/$skill $SKILLS_DIR/
done

# 3. Cleanup
rm -rf /tmp/wondelai-skills

# 4. Verify
hermes skills list | grep -E "clean-code|refactoring|software-design|clean-arch|system-design|ddia|release-it|pragmatic|domain-driven|mom-test"
```

### 4.3 Profile Binding (via symlinks)

```bash
# EDISON
for skill in clean-code refactoring-patterns software-design-philosophy \
             pragmatic-programmer domain-driven-design ddia-systems; do
  ln -sf ~/.hermes/skills/wondelai/$skill \
         ~/.hermes/profiles/edison/skills/wondelai/$skill
done

# SHAKA
for skill in clean-architecture system-design ddia-systems \
             software-design-philosophy domain-driven-design; do
  ln -sf ~/.hermes/skills/wondelai/$skill \
         ~/.hermes/profiles/shaka/skills/wondelai/$skill
done

# LILITH
ln -sf ~/.hermes/skills/wondelai/release-it \
       ~/.hermes/profiles/lilith/skills/wondelai/release-it

# KUMACHII
for skill in pragmatic-programmer mom-test; do
  ln -sf ~/.hermes/skills/wondelai/$skill \
         ~/.hermes/profiles/kumachii/skills/wondelai/$skill
done

# BONNEY
for skill in pragmatic-programmer mom-test; do
  ln -sf ~/.hermes/skills/wondelai/$skill \
         ~/.hermes/profiles/bonney/skills/wondelai/$skill
done
```

### 4.4 Medium-Fit (install kalau ada need)

| Skill | Target | When |
|---|---|---|
| `jobs-to-be-done` | KUMACHII | Kalau Gilang start product planning |
| `lean-startup` | KUMACHII | Experiment-driven project |
| `refactoring-ui` | EDISON | UI project aktif |
| `negotiation` | KUMACHII | Freelance/client negotiation |
| `storybrand-messaging` | BONNEY | Technical writing enhancement |

---

## Bagian 5 — Pin Load-Bearing Skills via Curator

### 5.1 Apa itu Curator?

Curator adalah Hermes built-in background process yang:
- Monitor semua skills di profile
- Track kapan terakhir kali skill dipakai (`_iters_since_skill` counter)
- Kalau skill **tidak dipakai selama N hari** (default 7) → mark `stale`
- Kalau stale terlalu lama (default 30 hari) → `archive`
- Kalau archived terlalu lama (default 90 hari) → bisa di-delete

**Tujuan**: Prevent skill bloat, keep skill inventory relevant.

**Masalah**: Curator bisa archive skill yang CRITICAL tapi jarang dipakai (contoh: `handoff-template` dipakai cuma saat handoff, bisa berhari-hari idle). Ini yang disebut **load-bearing skills** — jarang dipakai tapi kalau hilang, workflow rusak.

### 5.2 Pin = Protection dari Curator

Pin = flag khusus yang bikin curator **SKIP skill itu** dari lifecycle management:

```
active → [idle 7 hari] → stale → [idle 30 hari] → archived
                       ↑
                  PINNED = never stale, never archived
```

### 5.3 Contoh Pin Commands

```bash
# Pin shared framework skills (SEMUA profiles perlu ini)
hermes -p kumachii curator pin handoff-template
hermes -p kumachii curator pin grill-me
hermes -p stella curator pin gate-checklist
hermes -p stella curator pin handoff-template
hermes -p stella curator pin grill-me
hermes -p edison curator pin test-driven-development
hermes -p edison curator pin handoff-template
hermes -p atlas curator pin atlas-security-review
hermes -p atlas curator pin atlas-block-release
hermes -p york curator pin york-block-release
hermes -p york curator pin qa-review
hermes -p lilith curator pin lilith-deployment
hermes -p bonney curator pin bonney-rag-pipeline
hermes -p stussy curator pin stussy-health-check
hermes -p shaka curator pin shaka-architecture
```

### 5.4 Curator Status Check

```bash
# Cek curator state per profile
hermes -p kumachii curator status
hermes -p stella curator status

# Cek semua pinned skills
hermes -p kumachii curator list --pinned

# Manual archive (kalau mau cleanup)
hermes -p kumachii curator archive skill-name
```

### 5.5 Curator Config per Profile

```yaml
# ~/.hermes/profiles/<name>/config.yaml
curator:
  enabled: true
  interval_hours: 168        # check setiap 7 hari
  min_idle_hours: 2          # minimum idle sebelum eligible
  stale_after_days: 30       # active → stale
  archive_after_days: 90     # stale → archived
  backup:
    enabled: true
    keep: 5                  # backup 5 arsip terakhir
```

### 5.6 Pin Automation Script

Untuk batch-pin semua load-bearing skills:

```bash
#!/bin/bash
# pin-loadbearing.sh — Pin critical skills per profile

declare -A PINS=(
  ["kumachii"]="handoff-template grill-me kumachii-handoff kumachii-intake"
  ["stella"]="handoff-template grill-me gate-checklist stella-orchestration stella-gate-enforcer stella-delegation-router"
  ["edison"]="handoff-template grill-me test-driven-development edison-atomic-commits github-pr-workflow"
  ["pythagoras"]="handoff-template grill-me pythagoras-research-report"
  ["atlas"]="handoff-template grill-me atlas-security-review atlas-block-release security-review"
  ["york"]="handoff-template grill-me york-review-checklist york-block-release qa-review"
  ["lilith"]="handoff-template grill-me lilith-deployment deployment-check"
  ["bonney"]="handoff-template grill-me bonney-rag-pipeline rag-ingestion"
  ["stussy"]="handoff-template grill-me stussy-health-check stussy-incident-response"
  ["shaka"]="handoff-template grill-me shaka-architecture shaka-adr"
)

for profile in "${!PINS[@]}"; do
  echo "=== Pinning skills for $profile ==="
  for skill in ${PINS[$profile]}; do
    hermes -p "$profile" curator pin "$skill" 2>/dev/null && \
      echo "  [OK] $skill" || \
      echo "  [SKIP] $skill (not found or already pinned)"
  done
done
```

---

## Execution Order

| Phase | Task | Dependency | Estimate |
|---|---|---|---|
| 0 | Gilang review & approve this plan | None | - |
| 1 | Propagate global skills (symlink strategy) | Phase 0 | 15 min |
| 2 | Disable off-role skills per profile (skills.disabled) | Phase 1 | 20 min |
| 3 | Install wondelai 10 high-fit skills | Phase 0 | 10 min |
| 4 | Patch SOUL.md mandatory skills section | Phase 1, 2 | 30 min |
| 5 | Pin load-bearing skills via curator | Phase 4 | 15 min |
| 6 | Test: `hermes -p <profile> chat -q "list your mandatory skills"` | Phase 5 | 20 min |

---

## Risks & Mitigasi

| Risk | Impact | Mitigation |
|---|---|---|
| Symlink break on `hermes update` | Medium | Document symlinks, add to backup |
| skills.disabled typo = skill invisible | High | Test per profile after disable |
| Curator archive unpinned skill | Medium | Pin BEFORE enabling curator aggressive mode |
| wondelai skill conflict with built-in | Low | Install under `wondelai/` namespace |
| 10 profiles x 10 min test = 100 min | Medium | Batch test script |
