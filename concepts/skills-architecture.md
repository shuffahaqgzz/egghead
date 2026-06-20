---
title: Skills Architecture and Adoption
created: 2026-06-20
updated: 2026-06-20
type: concept
tags:
  - skills
  - architecture
  - adoption
  - propagation
  - profiles
sources:
  - kumachii-stella-skills-architecture-plan-2026-05-21.md
  - agent-skills-mandate-audit-2026-05-18.md
  - hermes-skill-adoption-plan-2026-05-26.md
  - hermes-skill-adoption-rollout-2026-05-26.md
---

# Skills Architecture and Adoption

Plan for propagating global skills to all 10 non-default Hermes profiles, ensuring mandatory skill loading via SOUL.md mandates, and integrating high-fit external skills.

## Problem Statement

All 10 profile specialists have ~79 identical boilerplate skills from global `~/.hermes/skills/`, but:
- No automatic propagation when new skills are added globally
- Role-specific skills (e.g., `stella-gate-enforcer`, `edison-atomic-commits`) not available in profiles
- Shared load-bearing skills (`9router`, `hermes-wiki`, `honcho-self-host`) missing from profiles
- Directory-level overrides blocking visibility (e.g., kumachii's local `multi-agent-framework/` blocks global)

## Current State

| Profile | Local Skills | Missing from Global |
|---|---|---|
| default (hermes) | 123 (global) | n/a |
| kumachii | 90 | 9router, hermes-wiki, honcho-self-host, role-specific |
| stella | 92 | 9router, hermes-wiki, honcho-self-host, role-specific |
| edison | 79 | 9router, hermes-wiki, ALL role-specific |
| atlas | 79 | 9router, hermes-wiki, ALL role-specific |
| bonney | 79 | 9router, hermes-wiki, ALL role-specific |
| lilith | 79 | 9router, hermes-wiki, ALL role-specific |
| pythagoras | 79 | 9router, hermes-wiki, ALL role-specific |
| shaka | 80 | 9router, hermes-wiki, ALL role-specific |
| stussy | 80 | 9router, hermes-wiki, ALL role-specific |
| york | 80 | 9router, hermes-wiki, ALL role-specific |

## Propagation Strategy

### Option A: Symlink (Recommended)
Symlink from profile local dir → global skill dir. Auto-sync on global update.

```bash
PROFILE=edison
PROFILE_SKILLS=~/.hermes/profiles/$PROFILE/skills
for skill in 9router hermes-wiki honcho-self-host; do
  ln -sf ~/.hermes/skills/multi-agent-framework/$skill \
         $PROFILE_SKILLS/multi-agent-framework/$skill
done
```

### Option B: `skills.external_dirs` in Config
Add global skills dir to profile config:
```yaml
skills:
  external_dirs:
    - /home/infra/.hermes/skills
```

## Universal Shared Skills

These must be present and mandated across all 10 profiles:

| Skill | Trigger | Reason |
|---|---|---|
| `handoff-template` | Any handoff, delegation, or report-back | Structured inter-agent work |
| `discord-routing` | Any Discord channel routing | Prevents wrong-channel routing |
| `9router` | Any LLM gateway call through 127.0.0.1:20128 | Prevents unsafe gateway use |
| `hermes-wiki` | Hermes architecture/design questions | Verified Hermes internals |
| `honcho-self-host` | Memory backend workflows | Standardizes memory operations |
| `grill-me` | Before declaring non-trivial work complete | Forces self-review |
| `gate-checklist` | Gate transition, approval, deployment readiness | Framework gate enforcement |

## Per-Profile Mandatory Skills

| Profile | Mandatory Skills | Primary Triggers |
|---|---|---|
| kumachii | `kumachii-intake`, `kumachii-handoff`, `kumachii-content`, `kumachii-finance` | Intake classification, personal tasks, content drafting |
| stella | `stella-orchestration`, `stella-delegation-router`, `stella-gate-enforcer`, `stella-decision-log` | Orchestration, delegation, gate enforcement |
| edison | `edison-story-impl`, `edison-atomic-commits`, `tdd` | Story implementation, atomic commits, TDD |
| shaka | `shaka-architecture`, `shaka-adr`, `shaka-readiness-check` | Architecture design, ADRs, readiness |
| atlas | `atlas-threat-model`, `atlas-security-review`, `atlas-block-release` | Threat modeling, security review, blocking |
| york | `york-review-checklist`, `york-acceptance-validation`, `york-block-release` | Code review, acceptance validation |
| lilith | `lilith-deployment`, `lilith-rollback`, `lilith-commissioning` | Deployment, rollback, commissioning |
| bonney | `bonney-rag-pipeline`, `bonney-docs-standards`, `bonney-source-hierarchy` | RAG ingestion, documentation, source hierarchy |
| stussy | `stussy-health-check`, `stussy-incident-response`, `stussy-runbook` | Health checks, incident response, runbooks |
| pythagoras | `pythagoras-research-report`, `pythagoras-comparison` | Research reports, comparisons |

## Skills Mandate Audit

**Audit Date:** 2026-05-18
**Scope:** All 10 non-default profiles
**Verdict:** **FAILED** — Skills available on disk but not mandated in SOUL.md

### Audit Results
| Profile | SOUL Lines | Skills on Disk | Skill Mentions in SOUL | Role-Specific (not mandated) |
|---|---|---|---|---|
| kumachii | 75 | 98 | **0 (none)** | 6 skills |
| stella | 261 | 100 | 1 (`handoff-template`) | 11 skills |
| edison | 208 | 92 | 1 (`handoff-template`) | 5 skills |
| shaka | 240 | 89 | 1 (`handoff-template`) | 4 skills |
| atlas | 280 | 90 | 1 (`handoff-template`) | 2 skills |
| york | 258 | 92 | 1 (`handoff-template`) | 5 skills |
| lilith | 223 | 91 | 1 (`handoff-template`) | 4 skills |
| bonney | 239 | 90 | 1 (`handoff-template`) | 5 skills |
| pythagoras | 238 | 91 | 1 (`handoff-template`) | 6 skills |
| stussy | 251 | 91 | 1 (`handoff-template`) | 4 skills |

### Gap Analysis
1. **No "Skills (Mandatory)" section** in SOUL.md (like default profile's `9router` pattern)
2. **No `WAJIB load skill X SEBELUM Y`** mandates
3. **Role-specific skills never mentioned** in SOUL.md
4. **KUMACHII totally empty** — 75 lines, zero skill mentions

### Patch Plan
Add `## Skills (MANDATORY)` section to each SOUL.md before `## Behavior Rules`. Example for KUMACHII:
```markdown
## Skills (MANDATORY)
- Every intake classification → `kumachii-intake`
- Content creation → `kumachii-content`
- Finance tracking → `kumachii-finance`
- Handoff to STELLA → `kumachii-handoff`
```

## Adoption Principles

1. **Progressive disclosure** — trigger-based, not load-everything
2. **Mandatory triggers** — SOUL.md says exactly which skill to load
3. **Profile isolation** — each profile sees only relevant skills
4. **Shared baseline** — all profiles need core coordination skills
5. **Skill pinning** — pin load-bearing skills before curator cleanup
6. **Minimal blast radius** — propagate one profile class at a time
7. **No destructive rollout** — prefer symlink/copy additions

## Related Pages

- [[multi-agent-workflow-framework]] - Framework specification
- [[deployment-and-operations]] - Deployment status and skills inventory
- [[hermes-wiki-research]] - Hermes-Wiki architecture reference
