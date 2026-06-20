# Hermes Skill Adoption Rollout - 2026-05-26

Status: Applied and verified
Plan: /home/infra/.hermes/knowledge/topics/hermes-skill-adoption-plan-2026-05-26.md
Backup path: /home/infra/.hermes/backups/skill-adoption-20260526-impl/

## Changes Applied

- Added Skills (MANDATORY) sections to all 10 non-default profile SOUL.md files.
- Added shared skill symlinks for all profiles where missing:
  - multi-agent-framework/9router
  - multi-agent-framework/hermes-wiki
  - multi-agent-framework/honcho-self-host
  - multi-agent-framework-shared/handoff-template
  - multi-agent-framework-shared/discord-routing
  - multi-agent-framework-shared/handoff-report-back
  - multi-agent-framework-shared/grill-me
  - multi-agent-framework-shared/gate-checklist
- Installed 10 Wondelai high-fit skills under /home/infra/.hermes/skills/wondelai/.
- Bound Wondelai skills to target profiles via profile-local symlinks.
- Added missing role-specific symlinks from global skills into target profiles.
- Updated profiles/stella/config.yaml delegation max_spawn_depth from 1 to 2.
- Pinned all mandatory skills per profile through hermes curator pin.

## Files Backed Up

Backups were created before edits for:

- profiles/kumachii/SOUL.md
- profiles/stella/SOUL.md
- profiles/edison/SOUL.md
- profiles/shaka/SOUL.md
- profiles/atlas/SOUL.md
- profiles/york/SOUL.md
- profiles/lilith/SOUL.md
- profiles/bonney/SOUL.md
- profiles/pythagoras/SOUL.md
- profiles/stussy/SOUL.md
- profiles/stella/config.yaml

## Verification

Passed commands:

    rtk python3 scripts/verify-maf-deploy-ready.py --fixture --full-lifecycle
    rtk python3 scripts/verify-maf-deploy-ready.py --fixture --full-lifecycle --live

Additional checks passed:

- All 10 profiles contain Skills (MANDATORY).
- All mandatory skills referenced in those sections resolve to a local profile skill path.
- hermes skills list --enabled-only succeeds for all 10 profiles.
- Shared mandatory skills appear in all 10 profile skill registries.
- EDISON registry shows Wondelai bindings including clean-code, ddia-systems, domain-driven-design, pragmatic-programmer, refactoring-patterns, and software-design-philosophy.
- Curator pinning returned success for all mandatory skill/profile combinations.

## Deviations

- tdd was not present as an installed skill in this workspace. EDISON uses test-driven-development; YORK was changed from tdd to test-driven-development for the TDD validation trigger.
- health-watchdog-cronjob was not present as an installed skill in this workspace. STUSSY uses stussy-health-check, stussy-incident-response, stussy-runbook, and deployment-check for monitoring and verification.
- Full hermes skills inspect for every mandatory skill was attempted. It timed out while inspecting 9router in the second profile, so verification relies on filesystem resolution, hermes skills list --enabled-only, curator pin success, and deploy-ready verifier output.

## Rollback

To roll back profile instruction/config edits, restore from:

    /home/infra/.hermes/backups/skill-adoption-20260526-impl/

Symlink additions are reversible by removing the specific profile skill symlinks created during this rollout. The Wondelai source copy remains under /home/infra/.hermes/skills/wondelai/.

## Notes

- Temporary clone source remains at /tmp/wondelai-skills-20260526. It was left in place to avoid running destructive cleanup without explicit approval.
- /home/infra/.hermes is not a Git repository, so there is no git diff or commit for this rollout.
