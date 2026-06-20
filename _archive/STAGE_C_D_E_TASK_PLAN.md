# Stage C-D-E Task Plan — Per-Agent Breakdown

**Version:** 1.0.0
**Date:** 2026-05-14
**Status:** Approved plan, ready for execution
**Prerequisite:** Stage A-B complete (KUMACHII, STELLA, EDISON, PYTHAGORAS deployed & verified)

---

## Agent 1: KUMACHII — Personal Assistant + Intake

### Task K-1: Adopt existing Hermes skills
- [ ] Link/copy `google-workspace` skill ke profile
- [ ] Link/copy `himalaya` skill ke profile
- [ ] Link/copy `obsidian` skill ke profile
- [ ] Link/copy `spotify` skill ke profile
- [ ] Link/copy `maps` skill ke profile
- [ ] Link/copy `airtable` skill ke profile
- [ ] Link/copy `notion` skill ke profile
- [ ] Link/copy `ocr-and-documents` skill ke profile

### Task K-2: Create custom skill `kumachii-intake`
- [ ] Classify request type (personal vs project)
- [ ] Personal → handle directly
- [ ] Project → create task-brief.md → handoff ke STELLA
- [ ] Template: task-brief format (goal, context, constraints, success criteria)
- [ ] Route decision logic (simple personal / complex personal / project)

### Task K-3: Create custom skill `kumachii-finance`
- [ ] Personal finance organization templates
- [ ] Budget tracking format
- [ ] Monthly summary generation
- [ ] Integration notes (spreadsheet, Airtable)

### Task K-4: Create custom skill `kumachii-content`
- [ ] Email draft templates (formal, casual, follow-up)
- [ ] Meeting notes format
- [ ] Content creation guidelines (blog, social, docs)
- [ ] Tone/style adaptation rules

### Task K-5: Verify toolset config
- [ ] Confirm: NO terminal, NO code_execution
- [ ] Confirm: HAS browser, web, file, messaging, delegation, cronjob, image_gen, tts
- [ ] Smoke test: personal task, handoff, boundary rejection

---

## Agent 2: STELLA — Orchestrator + Workflow Owner

### Task S-1: Adopt existing Hermes skills
- [ ] Link/copy `kanban-orchestrator` skill ke profile
- [ ] Link/copy `writing-plans` skill ke profile
- [ ] Link/copy `multi-spec-orchestration` skill ke profile
- [ ] Link/copy `linear` skill ke profile (jika pakai)
- [ ] Link/copy `github-pr-workflow` skill ke profile
- [ ] Link/copy `github-issues` skill ke profile

### Task S-2: Create custom skill `stella-gate-enforcer`
- [ ] Gate 0-6 checklist templates (from framework Section 19)
- [ ] Phase transition logic (current phase → check gate → next phase)
- [ ] Block rules: FAIL = stuck, CONCERNS = proceed with monitor, PASS = next
- [ ] Human approval hooks (Gate 2, 4, 5 mandatory)
- [ ] Gate document generation (auto-fill from task context)

### Task S-3: Create custom skill `stella-delegation-router`
- [ ] Task classification → agent selection matrix (from Section 9)
- [ ] Handoff packet template (from Section 10)
- [ ] Priority assignment logic
- [ ] SLA assignment per task type
- [ ] Support agent selection (who assists the owner)
- [ ] Reviewer assignment

### Task S-4: Create custom skill `stella-decision-log`
- [ ] Decision log format (decision, reasoning, alternatives, date, owner)
- [ ] Approval tracking (requested, approved, rejected, deferred)
- [ ] Persistent storage location (`docs/decisions/`)
- [ ] Query interface (search past decisions)

### Task S-5: Verify toolset config
- [ ] Confirm: HAS terminal (limited), file, messaging, delegation, cronjob, web
- [ ] Confirm: NO code_execution, NO browser
- [ ] Smoke test: receive handoff, delegate, enforce gate, block tier 3

---

## Agent 3: SHAKA — Architect (Stage E — deploy later)

### Task SH-1: Create Hermes profile
- [ ] `hermes profile create shaka`
- [ ] Configure Discord bot + channel (1502666915808542951)
- [ ] Configure model (strongest reasoning)
- [ ] Create systemd service `hermes-gateway-shaka.service`
- [ ] Set DISCORD_ALLOW_BOTS=all, ALLOWED_USERS includes Stella bot ID

### Task SH-2: Configure toolset
- [ ] file (read all + write architecture docs)
- [ ] terminal (limited — diagram generation, git)
- [ ] web (research for tech decisions)
- [ ] memory, session_search, skills, todo
- [ ] messaging (report back to STELLA)
- [ ] clarify
- [ ] NO code_execution, NO delegation, NO cronjob

### Task SH-3: Adopt existing Hermes skills
- [ ] Link `writing-plans`
- [ ] Link `plan`
- [ ] Link `architecture-diagram`
- [ ] Link `excalidraw`
- [ ] Link `arxiv`
- [ ] Link `codebase-inspection`

### Task SH-4: Create custom skill `shaka-architecture`
- [ ] Architecture document template (components, data flow, API contracts, storage, security, deployment)
- [ ] System boundary definition format
- [ ] Component interaction diagram generation
- [ ] Implementation readiness checklist

### Task SH-5: Create custom skill `shaka-adr`
- [ ] ADR template (title, status, context, decision, consequences)
- [ ] ADR numbering convention
- [ ] ADR lifecycle (proposed → accepted → deprecated → superseded)
- [ ] Link to architecture doc

### Task SH-6: Create custom skill `shaka-readiness-check`
- [ ] Pre-implementation validation checklist
- [ ] Architecture coverage check (all stories have arch backing)
- [ ] Dependency approval check
- [ ] Security model coverage check

---

## Agent 4: EDISON — Coding Agent

### Task E-1: Adopt existing Hermes skills
- [ ] Link `test-driven-development`
- [ ] Link `systematic-debugging`
- [ ] Link `github-pr-workflow`
- [ ] Link `requesting-code-review`
- [ ] Link `python-debugpy`
- [ ] Link `node-inspect-debugger`
- [ ] Link `codebase-inspection`
- [ ] Link `github-code-review`

### Task E-2: Create custom skill `edison-story-impl`
- [ ] Per-story workflow:
  1. Read story + acceptance criteria
  2. Read related architecture section
  3. Write failing test (RED)
  4. Implement minimum code (GREEN)
  5. Run test suite
  6. Fix failures
  7. Refactor if needed (REFACTOR)
  8. Run full relevant test suite (VERIFY)
  9. Update docs if needed
  10. Create atomic commit
  11. Generate handoff to YORK (REVIEW)
- [ ] Story completion checklist
- [ ] Architecture boundary check

### Task E-3: Create custom skill `edison-atomic-commits`
- [ ] Conventional commit format (feat/fix/refactor/test/docs)
- [ ] One logical change per commit
- [ ] Commit message template
- [ ] Pre-commit checks (lint, test, no secrets)
- [ ] Branch naming convention

### Task E-4: Verify toolset config
- [ ] Confirm: HAS terminal, code_execution, file, delegation, messaging, web
- [ ] Confirm: NO browser, NO cronjob, NO image_gen, NO tts
- [ ] Smoke test: TDD cycle, commit, messaging back to STELLA

---

## Agent 5: PYTHAGORAS — Research Agent

### Task P-1: Adopt existing Hermes skills
- [ ] Link `arxiv`
- [ ] Link `youtube-content`
- [ ] Link `blogwatcher`
- [ ] Link `polymarket`
- [ ] Link `llm-wiki`

### Task P-2: Create custom skill `pythagoras-research-report`
- [ ] Structured output format:
  - Executive summary
  - Sources consulted (with citations)
  - Facts (verified)
  - Inferences (clearly marked)
  - Uncertainty areas
  - Recommendation with reasoning
  - Confidence level (high/medium/low)
- [ ] Source validation rules
- [ ] Freshness requirements
- [ ] Output file location (`docs/research/`)

### Task P-3: Create custom skill `pythagoras-comparison`
- [ ] Comparison template:
  - Criteria matrix (weighted)
  - Per-option analysis (pros, cons, risks)
  - Benchmark data (if available)
  - Cost analysis
  - Recommendation with reasoning
  - Decision-ready summary for STELLA
- [ ] Tool/framework comparison format
- [ ] Database comparison format
- [ ] Architecture option comparison format

### Task P-4: Verify toolset config
- [ ] Confirm: HAS browser, web, file, messaging, memory, session_search
- [ ] Confirm: NO terminal, NO code_execution, NO delegation, NO cronjob
- [ ] Smoke test: research task, write report file, message STELLA

---

## Agent 6: ATLAS — Security Advisor (Stage C)

### Task A-1: Create Hermes profile
- [ ] `hermes profile create atlas`
- [ ] Configure Discord bot + channel (1501447906396344320)
- [ ] Configure model (security reasoning)
- [ ] Create systemd service `hermes-gateway-atlas.service`
- [ ] Set DISCORD_ALLOW_BOTS=all, ALLOWED_USERS

### Task A-2: Configure toolset
- [ ] file (read all + write security docs)
- [ ] terminal (limited — security scans, dependency audit)
- [ ] web (CVE lookup, security advisories)
- [ ] memory, session_search, skills, todo
- [ ] messaging (report to STELLA, block notifications)
- [ ] clarify
- [ ] NO code_execution, NO delegation, NO cronjob

### Task A-3: Adopt existing Hermes skills
- [ ] Link `requesting-code-review`
- [ ] Link `github-code-review`
- [ ] Link `dogfood`

### Task A-4: Create custom skill `atlas-threat-model`
- [ ] STRIDE threat modeling template
- [ ] Asset identification
- [ ] Threat enumeration
- [ ] Risk scoring (likelihood x impact)
- [ ] Mitigation recommendations
- [ ] Output: `docs/security/threat-model.md`

### Task A-5: Create custom skill `atlas-security-review`
- [ ] Credential scan checklist
- [ ] Access control review template
- [ ] Dependency audit (CVE check, license check)
- [ ] Input validation review
- [ ] Output: `docs/security/security-review-*.md`

### Task A-6: Create custom skill `atlas-block-release`
- [ ] Block criteria (exposed secrets, critical CVE, broken access control, unsafe deploy config)
- [ ] Block notification format to STELLA
- [ ] Exception request process (requires human approval)
- [ ] Unblock verification checklist

---

## Agent 7: YORK — QA / Reviewer (Stage C)

### Task Y-1: Create Hermes profile
- [ ] `hermes profile create york`
- [ ] Configure Discord bot + channel (1502667093571534958)
- [ ] Configure model (strict reviewer)
- [ ] Create systemd service `hermes-gateway-york.service`
- [ ] Set DISCORD_ALLOW_BOTS=all, ALLOWED_USERS

### Task Y-2: Configure toolset
- [ ] file (read all + write review docs)
- [ ] terminal (test execution only)
- [ ] code_execution (test only)
- [ ] web (limited — docs lookup)
- [ ] memory, session_search, skills, todo
- [ ] messaging (report to STELLA, block notifications)
- [ ] clarify
- [ ] NO delegation, NO cronjob, NO browser

### Task Y-3: Adopt existing Hermes skills
- [ ] Link `requesting-code-review`
- [ ] Link `github-code-review`
- [ ] Link `dogfood`
- [ ] Link `test-driven-development`
- [ ] Link `codebase-inspection`

### Task Y-4: Create custom skill `york-review-checklist`
- [ ] Review dimensions:
  - Correctness
  - Tests (coverage, edge cases)
  - Security
  - Architecture compliance
  - Naming consistency
  - Unnecessary complexity
  - Documentation impact
  - Observability impact
- [ ] Review report template
- [ ] Defect logging format
- [ ] Output: `docs/reviews/code-review-*.md`

### Task Y-5: Create custom skill `york-acceptance-validation`
- [ ] Acceptance criteria verification process
- [ ] Test result validation
- [ ] Regression check
- [ ] User-facing impact assessment

### Task Y-6: Create custom skill `york-block-release`
- [ ] Block criteria (failed AC, failed tests, unreviewed critical code, incomplete docs)
- [ ] Block notification format
- [ ] Retest-after-fix process
- [ ] Unblock verification

---

## Agent 8: LILITH — DevOps (Stage D)

### Task L-1: Create Hermes profile
- [ ] `hermes profile create lilith`
- [ ] Configure Discord bot + channel (1502667034532774041)
- [ ] Configure model (DevOps/infra reasoning)
- [ ] Create systemd service `hermes-gateway-lilith.service`
- [ ] Set DISCORD_ALLOW_BOTS=all, ALLOWED_USERS

### Task L-2: Configure toolset
- [ ] file (read all + write deploy docs/config)
- [ ] terminal (full — build, deploy, infra commands)
- [ ] web (limited — docs, registry lookup)
- [ ] memory, session_search, skills, todo
- [ ] messaging (notify STELLA, request approval)
- [ ] cronjob (scheduled deployments, health checks)
- [ ] clarify
- [ ] NO code_execution, NO delegation, NO browser

### Task L-3: Adopt existing Hermes skills
- [ ] Link `github-pr-workflow`
- [ ] Link `github-repo-management`

### Task L-4: Create custom skill `lilith-deployment`
- [ ] Pre-flight checklist (branch, tests, staging green, changelog, deps, secrets scan)
- [ ] Deployment strategies (blue-green, canary, rolling)
- [ ] Staging deployment flow
- [ ] Production approval request format
- [ ] Post-deployment validation
- [ ] Output: `docs/deployment.md`, `docs/commissioning-report.md`

### Task L-5: Create custom skill `lilith-rollback`
- [ ] Auto-rollback triggers (health check fail, error rate spike, latency spike)
- [ ] Manual rollback triggers
- [ ] Rollback steps (stop → revert → monitor → notify → investigate)
- [ ] Duration target (<12min total)
- [ ] Output: `docs/rollback-report.md`

### Task L-6: Create custom skill `lilith-commissioning`
- [ ] Commissioning test template
- [ ] Release checklist
- [ ] Environment validation
- [ ] Smoke test suite
- [ ] Runbook generation/update

---

## Agent 9: BONNEY — Documentation + RAG (Stage D)

### Task B-1: Create Hermes profile
- [ ] `hermes profile create bonney`
- [ ] Configure Discord bot + channel (1502926214413811792)
- [ ] Configure model (documentation, summarization)
- [ ] Create systemd service `hermes-gateway-bonney.service`
- [ ] Set DISCORD_ALLOW_BOTS=all, ALLOWED_USERS

### Task B-2: Configure toolset
- [ ] file (full write for docs + RAG)
- [ ] web (limited — source lookup)
- [ ] memory, session_search, skills, todo
- [ ] messaging (report to STELLA)
- [ ] clarify
- [ ] NO terminal, NO code_execution, NO delegation, NO cronjob, NO browser

### Task B-3: Adopt existing Hermes skills
- [ ] Link `obsidian`
- [ ] Link `ocr-and-documents`
- [ ] Link `youtube-content`

### Task B-4: Create custom skill `bonney-rag-pipeline`
- [ ] Pipeline: Ingest → Clean → Chunk → Tag → Embed → Index → Retrieve → Rerank → Cite → Validate
- [ ] Chunk metadata schema (source_file, source_type, owner_agent, created_at, project, phase, version, freshness, authority_level, contains_secret)
- [ ] Ingestion manifest format
- [ ] Retrieval test template
- [ ] Re-index triggers

### Task B-5: Create custom skill `bonney-docs-standards`
- [ ] Document templates per type (PRD, architecture, ADR, runbook, review, research)
- [ ] Metadata requirements
- [ ] Freshness tracking (fresh/stale/deprecated)
- [ ] Update triggers
- [ ] Style guide (compact, direct, no bluffing)

### Task B-6: Create custom skill `bonney-source-hierarchy`
- [ ] Authority ladder enforcement:
  1. Source-of-truth docs
  2. Architecture + ADRs
  3. PRD + acceptance criteria
  4. Code + tests
  5. Deployment + runbook
  6. Research reports
  7. Generated summaries
  8. Chat history
- [ ] Conflict resolution rules
- [ ] Override prevention (summary cannot override source)

---

## Agent 10: STUSSY — Monitoring + Ops (Stage D)

### Task ST-1: Create Hermes profile
- [ ] `hermes profile create stussy`
- [ ] Configure Discord bot + channel (1502926523789742191)
- [ ] Configure model (log analysis, ops reasoning)
- [ ] Create systemd service `hermes-gateway-stussy.service`
- [ ] Set DISCORD_ALLOW_BOTS=all, ALLOWED_USERS

### Task ST-2: Configure toolset
- [ ] file (read logs/metrics + write ops reports)
- [ ] terminal (monitoring commands only — systemctl status, journalctl, curl health)
- [ ] memory, session_search, skills, todo
- [ ] messaging (alert STELLA, notify team)
- [ ] cronjob (scheduled health checks, periodic monitoring)
- [ ] clarify
- [ ] NO code_execution, NO delegation, NO browser, NO web

### Task ST-3: Adopt existing Hermes skills
- [ ] Link `blogwatcher` (repurposed for log/alert monitoring)

### Task ST-4: Create custom skill `stussy-health-check`
- [ ] Service health check template (endpoint, expected response, frequency)
- [ ] Log summarization format
- [ ] Anomaly detection rules (error rate, latency, resource usage)
- [ ] Health report output: `docs/ops/health-report.md`
- [ ] Cron schedule for periodic checks

### Task ST-5: Create custom skill `stussy-incident-response`
- [ ] Incident classification (P1-P4 severity)
- [ ] Triage process
- [ ] Remediation recommendations
- [ ] Escalation rules (when to alert Gilang)
- [ ] Post-incident report template
- [ ] Output: `docs/ops/incident-report.md`

### Task ST-6: Create custom skill `stussy-runbook`
- [ ] Runbook template (service, health check, common issues, remediation steps)
- [ ] Update triggers (after incident, after deploy, after config change)
- [ ] Runbook validation (all services covered)
- [ ] Output: `docs/runbook.md`

### Task ST-7: Create custom skill `stussy-block-release`
- [ ] Block criteria (missing monitoring, missing runbook, recurring unresolved incident, failed smoke test)
- [ ] Block notification format
- [ ] Unblock verification

---

## Execution Order (Recommended)

```
Phase 1 — Enhance existing agents (Stage A-B already deployed):
  K-1 → K-2 → K-3 → K-4 → K-5  (KUMACHII skills)
  S-1 → S-2 → S-3 → S-4 → S-5  (STELLA skills)
  E-1 → E-2 → E-3 → E-4         (EDISON skills)
  P-1 → P-2 → P-3 → P-4         (PYTHAGORAS skills)

Phase 2 — Stage C (Control Layer):
  A-1 → A-2 → A-3 → A-4 → A-5 → A-6  (ATLAS deploy + skills)
  Y-1 → Y-2 → Y-3 → Y-4 → Y-5 → Y-6  (YORK deploy + skills)

Phase 3 — Stage D (Operate Layer):
  L-1 → L-2 → L-3 → L-4 → L-5 → L-6  (LILITH deploy + skills)
  B-1 → B-2 → B-3 → B-4 → B-5 → B-6  (BONNEY deploy + skills)
  ST-1 → ST-2 → ST-3 → ST-4 → ST-5 → ST-6 → ST-7  (STUSSY deploy + skills)

Phase 4 — Stage E (Architect Layer):
  SH-1 → SH-2 → SH-3 → SH-4 → SH-5 → SH-6  (SHAKA deploy + skills)
```

---

## Shared/Reusable Skills (Framework Section 32)

These should be created as global skills accessible by multiple agents:

| Skill | Used By | Purpose |
|---|---|---|
| `grill-me` | All agents | Self-review/challenge before handoff |
| `tdd` | EDISON, YORK | TDD enforcement template |
| `security-review` | ATLAS, YORK, LILITH | Security review template |
| `qa-review` | YORK, STELLA | QA review template |
| `deployment-check` | LILITH, STELLA, STUSSY | Deployment checklist |
| `rag-ingestion` | BONNEY | RAG ingestion workflow |
| `handoff-template` | All agents | Standardized handoff format |
| `gate-checklist` | STELLA (primary), all agents | Gate 0-6 templates |

---

## Total Task Count

| Agent | Adopt Skills | Create Skills | Profile Setup | Config | Total Tasks |
|---|---|---|---|---|---|
| KUMACHII | 8 | 3 | — (done) | 1 | 12 |
| STELLA | 6 | 3 | — (done) | 1 | 10 |
| EDISON | 8 | 2 | — (done) | 1 | 11 |
| PYTHAGORAS | 5 | 2 | — (done) | 1 | 8 |
| ATLAS | 3 | 3 | 1 | 1 | 8 |
| YORK | 5 | 3 | 1 | 1 | 10 |
| LILITH | 2 | 3 | 1 | 1 | 7 |
| BONNEY | 3 | 3 | 1 | 1 | 8 |
| STUSSY | 1 | 4 | 1 | 1 | 7 |
| SHAKA | 6 | 3 | 1 | 1 | 11 |
| **Shared** | — | 8 | — | — | 8 |
| **TOTAL** | **47** | **37** | **6** | **10** | **100** |

---

## Estimated Timeline

| Phase | Agents | Duration |
|---|---|---|
| Phase 1 (enhance existing) | KUMACHII, STELLA, EDISON, PYTHAGORAS | 3-4 days |
| Phase 2 (Stage C) | ATLAS, YORK | 3-4 days |
| Phase 3 (Stage D) | LILITH, BONNEY, STUSSY | 4-5 days |
| Phase 4 (Stage E) | SHAKA | 2-3 days |
| Shared skills | All | 2 days (parallel) |
| **Total** | | **~2-3 weeks** |
