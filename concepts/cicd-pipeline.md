---
title: "CI/CD Pipeline for Framework Changes"
created: 2026-06-20
updated: 2026-06-20
type: concept
tags: [cicd, pipeline, automation, framework, testing]
sources: []
confidence: high
---

# CI/CD Pipeline for Framework Changes

Automated pipeline untuk verify framework integrity saat ada perubahan.

## What Changes Trigger Pipeline

| Change Type | Files Affected | Pipeline Trigger |
|-------------|---------------|-----------------|
| SOUL update | `*_soul.md` in archive | Lint + wikilink check |
| Skill update | `skills/` directory | Skill validation |
| Schema update | `SCHEMA.md` | Tag audit + page validation |
| Wiki page update | `concepts/*.md`, `entities/*.md` | Lint + index check |
| Framework spec update | `egghead-framework.md` | Cross-reference check |

## Pipeline Stages

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│  1. LINT    │───▶│  2. TEST    │───▶│  3. VERIFY  │
│  Wiki lint  │    │  Skill load │    │  Cross-ref  │
│  Tag audit  │    │  SOUL parse │    │  Index check│
│  FM valid   │    │  Link check │    │  Schema ok  │
└─────────────┘    └─────────────┘    └─────────────┘
                                              │
                                              ▼
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│  6. REPORT  │◀───│  5. DEPLOY  │◀───│  4. APPROVE │
│  Summary    │    │  Git push   │    │  Gate check │
│  Metrics    │    │  Tag release│    │  Optional   │
│  Alerts     │    │  Notify     │    │  human gate │
└─────────────┘    └─────────────┘    └─────────────┘
```

## Stage Details

### Stage 1: Lint

```bash
# Wiki lint (existing script)
WIKI=~/egghead-wiki python3 lint-script.py

# Tag audit
# Check all tags in wiki pages are in SCHEMA.md taxonomy

# Frontmatter validation
# Check all required fields present

# Index completeness
# Check all pages listed in index.md
```

### Stage 2: Test

```bash
# Skill loading test
# For each skill in skills/:
#   - Verify SKILL.md exists
#   - Parse frontmatter
#   - Check required fields
#   - Verify referenced files exist

# SOUL parsing test
# For each *_soul.md in archive:
#   - Parse YAML frontmatter
#   - Verify agent, layer, platform, access fields
#   - Check workflow_phases are valid (0-6)

# Wikilink integrity
# Check all wikilinks point to existing pages
```

### Stage 3: Verify

```bash
# Cross-reference check
# Verify SOUL skills_used match skill-architecture.md entries
# Verify agent roles match agent-roster entries
# Verify workflow phases match workflow-lifecycle phases

# Schema compliance
# Verify SCHEMA.md tag taxonomy covers all used tags
# Verify page types match SCHEMA definitions
```

### Stage 4: Approve

```markdown
## Gate Decision

- [ ] All lint checks pass
- [ ] All tests pass
- [ ] All verifications pass
- [ ] No breaking changes (or breaking changes documented)

Decision: PASS / CONCERNS / FAIL
```

### Stage 5: Deploy

```bash
# Git operations
git add [changed files]
git commit -m "chore: framework update [description]"
git push origin main

# Optional: tag release
git tag -a v1.x.0 -m "Release description"
git push origin v1.x.0
```

### Stage 6: Report

```markdown
## Pipeline Report

**Run:** [timestamp]
**Trigger:** [what changed]
**Result:** PASS/FAIL

### Checks
- Lint: ✅ (0 issues)
- Test: ✅ (all skills load)
- Verify: ✅ (cross-refs ok)

### Changes
- Files changed: N
- New pages: N
- Updated pages: N
- Broken links: 0

### Metrics
- Lint time: Xs
- Test time: Xs
- Total time: Xs
```

## Implementation via Hermes Cron

```yaml
# Cron job: framework-integrity-check
schedule: "0 */6 * * *"  # Every 6 hours
prompt: |
  Run framework integrity check:
  1. Run wiki lint script
  2. Check all SOUL frontmatter
  3. Verify all wikilinks resolve
  4. Report any issues to Discord #ops channel
  5. If issues found, create incident report
```

## Implementation via Hermes Cron + Git Hook

```bash
# .git/hooks/pre-commit
#!/bin/bash
# Run lint before commit
WIKI=~/egghead-wiki python3 lint-script.py
if [ $? -ne 0 ]; then
  echo "Lint failed. Fix issues before committing."
  exit 1
fi
```

## See Also

- [[egghead-framework]] — Framework overview
- [[skill-architecture]] — Skill validation rules
