# Hermes Skill Adoption Plan - 10 Non-Default Profiles

**Date:** 2026-05-26  
**Status:** Planning source of truth before execution  
**Scope:** `kumachii`, `stella`, `edison`, `shaka`, `atlas`, `york`, `lilith`, `bonney`, `pythagoras`, `stussy`

---

## Executive Summary

This document consolidates the Hermes-Wiki research, mandatory skills audit, and Wondelai skill mapping into one adoption plan for the 10 non-default Hermes profiles.

Current problem:
- Skills already exist on disk, but most profiles do not have strict `SOUL.md` mandates that tell the agent when to load them.
- Role-specific skills such as `stella-gate-enforcer`, `edison-atomic-commits`, `pythagoras-research-report`, and `atlas-security-review` can be missed at runtime.
- Shared load-bearing skills such as `9router`, `hermes-wiki`, `handoff-template`, and `gate-checklist` need consistent profile-level visibility and curator pinning.
- Wondelai high-fit skills have been identified but are not yet installed or bound per profile.

Target outcome:
- Each profile has a trigger-based `## Skills (MANDATORY)` section in `SOUL.md`.
- Shared and role-specific skills resolve from the profile skill directory.
- Wondelai high-fit skills are installed under a controlled namespace and mapped only to profiles that need them.
- Load-bearing skills are pinned before aggressive curator cleanup.
- Runtime smoke tests prove each profile selects the expected skills for its role.

This plan is documentation only. Actual skill propagation, Wondelai install, `SOUL.md` patching, curator pinning, and smoke tests happen in a later execution step.

---

## Current Findings

Evidence from `agent-skills-mandate-audit-2026-05-18.md`:
- All 10 non-default profiles have local skills directories.
- `kumachii` currently has no skill mention in `SOUL.md`.
- The other 9 profiles mostly mention only `handoff-template`, and only for report-back behavior.
- No profile has a complete trigger-based mandatory skills section.
- Skills available on disk are not guaranteed to load unless the agent is explicitly told when to load them.

Evidence from `hermes-wiki-research-2026-05-18.md`:
- Hermes-Wiki should be treated as the architecture reference for multi-agent behavior, profile isolation, skill loading, curator pinning, and security patterns.
- Hermes profiles resolve state through `HERMES_HOME`, so profile-local skill visibility matters.
- Curator can archive idle skills unless load-bearing skills are pinned.
- Progressive disclosure is the intended model: skill metadata can be visible, but full skill content should load only when a trigger matches.

Evidence from `kumachii-stella-skills-architecture-plan-2026-05-21.md`:
- Shared skills such as `9router`, `hermes-wiki`, and `honcho-self-host` were missing from many non-default profiles during the previous audit.
- Wondelai has 10 high-fit skills for this framework, mostly for engineering, architecture, DevOps, intake, and documentation roles.
- Symlink-based propagation is the preferred strategy unless profile skill isolation needs a config-only approach.

---

## Source References

- `/home/infra/.hermes/knowledge/topics/hermes-wiki-research-2026-05-18.md`
- `/home/infra/.hermes/knowledge/topics/agent-skills-mandate-audit-2026-05-18.md`
- `/home/infra/.hermes/knowledge/topics/kumachii-stella-skills-architecture-plan-2026-05-21.md`
- `/home/infra/hermes-wiki/concepts/multi-agent-architecture.md`
- `/home/infra/hermes-wiki/concepts/skills-system-architecture.md`
- `/home/infra/hermes-wiki/concepts/configuration-and-profiles.md`
- `/home/infra/hermes-wiki/concepts/security-defense-system.md`
- `https://github.com/wondelai/skills`

---

## Adoption Principles

1. Progressive disclosure: keep skills trigger-based. Do not force every profile to load every skill on every task.
2. Mandatory triggers: `SOUL.md` must say exactly which skill to load before each role-critical workflow.
3. Profile isolation: each profile should only see or prefer skills relevant to its role.
4. Shared baseline: all profiles need the same core coordination, routing, memory, and self-review skills.
5. Skill pinning: pin load-bearing skills before enabling curator cleanup or idle archival.
6. Minimal execution blast radius: propagate and patch one profile class at a time, then smoke test.
7. No destructive rollout: do not delete old local skills during first adoption. Prefer symlink/copy additions and reversible `SOUL.md` patches.

---

## Universal Shared Skills

These skills should be present and mandated across all 10 non-default profiles.

| Skill | Trigger | Reason |
|---|---|---|
| `handoff-template` | Any handoff, delegation, or report-back | Keeps inter-agent work structured and file-backed. |
| `discord-routing` | Any Discord channel routing or cross-agent message | Prevents wrong-channel routing and ambiguous handoff targets. |
| `handoff-report-back` | Any phase report back to STELLA or parent agent | Keeps large reports out of single Discord messages. |
| `9router` | Any LLM gateway, model routing, image, TTS, STT, embedding, web-search, or web-fetch call through `127.0.0.1:20128` | Prevents unsafe or inconsistent gateway use. |
| `hermes-wiki` | Hermes architecture, profile, skills, curator, memory, tool, security, or gateway design question | Keeps design aligned with verified Hermes internals. |
| `honcho-self-host` | Memory backend, Honcho state, persistence, or self-hosted memory workflow | Standardizes memory operations. |
| `grill-me` | Before declaring non-trivial work complete | Forces self-review and challenge of assumptions. |
| `gate-checklist` | Gate transition, approval, deployment readiness, release/block decision | Standardizes framework gate enforcement. |

Pin all universal shared skills in every profile after they resolve locally.

---

## Per-Profile Mandatory Skills

| Profile | Mandatory skill set | Primary triggers |
|---|---|---|
| `kumachii` | `kumachii-intake`, `kumachii-handoff`, `kumachii-content`, `kumachii-finance`, `mom-test`, `pragmatic-programmer`, universal shared skills | Intake classification, personal assistant task routing, content drafting, finance categorization, customer/user interview questions, handoff to STELLA. |
| `stella` | `stella-orchestration`, `stella-delegation-router`, `stella-gate-enforcer`, `stella-decision-log`, `stella-redis-state`, universal shared skills | Multi-agent orchestration, specialist routing, gate enforcement, decision logging, Redis workflow state, delegated execution. |
| `edison` | `test-driven-development`, `tdd`, `edison-atomic-commits`, `edison-story-impl`, `systematic-debugging`, `clean-code`, `refactoring-patterns`, `software-design-philosophy`, `ddia-systems`, `pragmatic-programmer`, `domain-driven-design`, universal shared skills | TDD implementation, atomic commits, story implementation, debugging, refactoring, data-intensive design, domain modeling. |
| `shaka` | `shaka-architecture`, `shaka-adr`, `shaka-readiness-check`, `writing-plans`, `plan`, `clean-architecture`, `system-design`, `ddia-systems`, `software-design-philosophy`, `domain-driven-design`, universal shared skills | Architecture plan, ADR, readiness review, system design, module boundaries, distributed/data design. |
| `atlas` | `atlas-security-review`, `atlas-threat-model`, `atlas-block-release`, `security-review`, `github-code-review`, universal shared skills | Security review, threat modeling, sensitive code review, release blocking, security gate decisions. |
| `york` | `york-review-checklist`, `york-acceptance-validation`, `york-block-release`, `qa-review`, `requesting-code-review`, `tdd`, universal shared skills | QA review, acceptance validation, test coverage review, pre-merge review, release/block decision. |
| `lilith` | `lilith-deployment`, `lilith-commissioning`, `lilith-rollback`, `deployment-check`, `release-it`, `webhook-subscriptions`, universal shared skills | Deployment, commissioning, rollback, production readiness, webhook deployment, release hardening. |
| `bonney` | `bonney-docs-standards`, `bonney-rag-pipeline`, `bonney-source-hierarchy`, `rag-ingestion`, `ocr-and-documents`, `nano-pdf`, `obsidian`, `notion`, `mom-test`, `pragmatic-programmer`, universal shared skills | Documentation, RAG ingestion, source hierarchy, OCR/PDF workflows, knowledge base work, user research documentation. |
| `pythagoras` | `pythagoras-research-report`, `pythagoras-comparison`, `arxiv`, `blogwatcher`, `llm-wiki`, `polymarket`, universal shared skills | Research reports, comparison/benchmark, academic literature, blog/RSS monitoring, LLM knowledge base, market data. |
| `stussy` | `stussy-health-check`, `stussy-incident-response`, `stussy-runbook`, `stussy-block-release`, `deployment-check`, `webhook-subscriptions`, `health-watchdog-cronjob`, universal shared skills | Health checks, incident response, runbooks, deployment verification, monitoring, release/block decision. |

---

## Wondelai Adoption Table

Install these under a namespaced location such as `/home/infra/.hermes/skills/wondelai/<skill>` or profile-local `skills/wondelai/<skill>`.

| Wondelai skill | Target profile(s) | Mandatory trigger |
|---|---|---|
| `clean-code` | `edison` | Before implementing or reviewing code where readability, naming, function size, or maintainability matters. |
| `refactoring-patterns` | `edison` | Before refactoring existing behavior or naming a transformation strategy. |
| `software-design-philosophy` | `edison`, `shaka` | Before design decisions involving complexity, module depth, abstractions, or boundaries. |
| `clean-architecture` | `shaka` | Before defining layers, dependency direction, use cases, adapters, or service boundaries. |
| `system-design` | `shaka` | Before distributed systems, scaling, caching, queues, rate limits, storage, or availability design. |
| `ddia-systems` | `shaka`, `edison` | Before DB, eventing, replication, consistency, streaming, queue, or data model decisions. |
| `release-it` | `lilith` | Before production deployment, rollback planning, resilience review, timeout/circuit-breaker work. |
| `pragmatic-programmer` | `edison`, `kumachii`, `bonney` | Before broad craftsmanship decisions, documentation quality checks, or pragmatic task framing. |
| `domain-driven-design` | `shaka`, `edison` | Before feature modeling, bounded context decisions, ubiquitous language, or business domain scoping. |
| `mom-test` | `kumachii`, `bonney` | Before user/customer interview questions, requirement discovery, or non-leading research prompts. |

---

## Config and SOUL Impact

### `SOUL.md`

Add a `## Skills (MANDATORY)` section to every target profile.

Recommended pattern:

```markdown
## Skills (MANDATORY)

Load skills before starting work. Follow trigger -> load -> execute.

| Skill | Trigger |
|---|---|
| `9router` | Any LLM gateway, model routing, image, TTS, STT, embedding, web-search, or web-fetch call through `127.0.0.1:20128`. |
| `hermes-wiki` | Hermes architecture, profile, skills, curator, memory, tool, security, or gateway design question. |
| `handoff-template` | Any handoff, delegation, or report-back. |
| `grill-me` | Before declaring non-trivial work complete. |
| `<role-skill>` | `<role-specific trigger>`. |

Pin these skills. They must not be archived by curator.
```

### Profile Skill Directories

Each profile should resolve:
- Universal shared skills.
- Its own role-specific skills.
- Its assigned Wondelai skills only.

Preferred strategy:
- Use symlinks from profile skill directories to global skill directories for shared and Wondelai skills.
- Keep role-specific skills profile-local or symlinked only to the target profile.
- Avoid broad `external_dirs` unless skill noise is acceptable and allowlist/disable controls are in place.

### Curator

Before enabling aggressive cleanup:
- Pin universal shared skills in all profiles.
- Pin role-specific skills in the profile that depends on them.
- Pin Wondelai skills after installation and profile binding.

### STELLA Delegation Config

Recommended values:

```yaml
delegation:
  max_concurrent_children: 3
  max_spawn_depth: 2
  orchestrator_enabled: true
```

---

## Rollout Phases

1. Baseline verification  
   List skills for all 10 profiles and capture which universal, role-specific, and Wondelai skills are missing.

2. Propagate missing shared skills  
   Add or symlink `handoff-template`, `discord-routing`, `handoff-report-back`, `9router`, `hermes-wiki`, `honcho-self-host`, `grill-me`, and `gate-checklist`.

3. Install or bind Wondelai high-fit skills  
   Install the 10 selected Wondelai skills under a namespace and bind only to target profiles.

4. Patch each profile `SOUL.md`  
   Add `## Skills (MANDATORY)` with trigger-based rows for universal, role-specific, and Wondelai skills.

5. Pin load-bearing skills via curator  
   Pin universal shared skills in every profile, then pin role-specific and Wondelai skills per profile.

6. Run runtime smoke tests per profile  
   Use small prompts that should trigger mandatory skill selection without performing destructive work.

7. Update decision log or session summary  
   Record final adopted strategy, any deviations, verification evidence, and rollback notes.

---

## Verification Plan

### Documentation Validation

Checklist for this plan:
- All 10 profiles are represented.
- Universal skills are separated from role-specific skills.
- Every recommended Wondelai skill has a target profile and trigger.
- Config and `SOUL.md` impact are explicit.
- Rollout phases are ordered and reversible.
- Risks and rollback are documented.

### Baseline Commands

Run per profile:

```bash
rtk env HERMES_HOME=/home/infra/.hermes/profiles/<profile> hermes skills list
```

Framework verification:

```bash
rtk python3 scripts/verify-maf-deploy-ready.py --fixture --full-lifecycle
rtk python3 scripts/verify-maf-deploy-ready.py --fixture --full-lifecycle --live
```

### Smoke Test Scenarios

| Profile | Smoke scenario | Expected skill selection |
|---|---|---|
| `kumachii` | Classify an intake task and decide whether to route to STELLA. | `kumachii-intake`, `kumachii-handoff`, `handoff-template`; `mom-test` if discovery questions are needed. |
| `stella` | Route a complex task to one specialist and name gate requirements. | `stella-orchestration`, `stella-delegation-router`, `stella-gate-enforcer`, `gate-checklist`, `handoff-template`. |
| `edison` | Explain mandatory skills for a new coding task that must use TDD. | `test-driven-development`, `tdd`, `edison-atomic-commits`, `clean-code`. |
| `shaka` | Produce an architecture plan skill list for a distributed feature. | `shaka-architecture`, `shaka-adr`, `system-design`, `ddia-systems`, `hermes-wiki`. |
| `atlas` | Select skills for a security review and release-block decision. | `atlas-security-review`, `atlas-threat-model`, `atlas-block-release`, `security-review`. |
| `york` | Select QA skills for acceptance validation before merge. | `york-review-checklist`, `york-acceptance-validation`, `qa-review`, `requesting-code-review`. |
| `lilith` | Select deploy-check skills for a production rollout plan. | `lilith-deployment`, `deployment-check`, `release-it`, `lilith-rollback`. |
| `bonney` | Select docs/RAG skills for ingesting a PDF into knowledge base. | `bonney-rag-pipeline`, `bonney-source-hierarchy`, `rag-ingestion`, `ocr-and-documents` or `nano-pdf`. |
| `pythagoras` | Select research skills for a comparison report. | `pythagoras-research-report`, `pythagoras-comparison`, `arxiv` or `blogwatcher`, `llm-wiki`. |
| `stussy` | Select incident/health-check skills for an unhealthy service. | `stussy-health-check`, `stussy-incident-response`, `stussy-runbook`, `deployment-check`. |

---

## Risks and Mitigations

| Risk | Impact | Mitigation |
|---|---|---|
| Skill exists but profile cannot resolve it | Mandatory `SOUL.md` trigger points to missing skill | Baseline `hermes skills list` per profile before patching `SOUL.md`. |
| Curator archives load-bearing skills | Runtime skill selection degrades silently | Pin universal, role-specific, and Wondelai skills before cleanup. |
| Wondelai skill name collides with existing skill | Wrong skill loads or skill list becomes ambiguous | Install under `wondelai/` namespace or prefix with `wondelai-` if Hermes cannot namespace nested skills cleanly. |
| Profiles see too many irrelevant skills | Skill picker noise and wrong skill selection | Prefer per-profile symlinks over broad `external_dirs`; use allowlist/disable config if needed. |
| `SOUL.md` grows too verbose | Profile instruction bloat and reduced clarity | Use compact trigger tables, not long prose. |
| STELLA delegation depth creates uncontrolled child chains | Cost, loops, or state confusion | Use `max_concurrent_children: 3`, `max_spawn_depth: 2`, and smoke test before live use. |
| Wondelai content triggers Skills Guard false positive | Install fails or skill is blocked | Edit unsafe wording in the skill description if needed; do not disable guard globally. |
| Discord cross-agent loop | Agents repeatedly respond to each other | Keep routing skills mandatory, use dedup/rate limit/kill switch where available. |

---

## Rollback

Rollback should be file-level and profile-scoped.

1. Revert `SOUL.md` changes for the affected profile from backup or versioned patch.
2. Unpin newly pinned skills only if curator behavior must return to the previous state.
3. Remove Wondelai symlinks or profile-local Wondelai directories for the affected profile.
4. Leave global Wondelai skill source intact unless the install itself is faulty.
5. Re-run:

```bash
rtk env HERMES_HOME=/home/infra/.hermes/profiles/<profile> hermes skills list
```

6. Run the relevant smoke scenario again and confirm the profile no longer references removed skills.

---

## Acceptance Criteria for Later Execution

- All 10 profile `SOUL.md` files include `## Skills (MANDATORY)`.
- Every universal shared skill resolves in every target profile.
- Every role-specific skill resolves in its target profile.
- Every Wondelai high-fit skill resolves only in intended profile(s), unless a broader policy is explicitly chosen.
- Curator pins exist for universal shared skills and profile-critical role skills.
- STELLA config has documented delegation values or an explicit reason for divergence.
- Runtime smoke tests show each profile selects the expected mandatory skills.
- Decision log or session summary records what changed, verification evidence, and rollback path.
