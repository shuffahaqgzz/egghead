---
title: "Workflow Lifecycle"
created: 2026-06-20
updated: 2026-06-20
type: concept
tags: [workflow, lifecycle, phases, gates, orchestration]
sources: []
confidence: high
---

# Workflow Lifecycle

Detailed phase documentation untuk [[egghead-framework]] 7-phase workflow.

## Overview

```
Phase 0: DISCOVERY    → Owner: Intake/Orchestrator → Gate 0
Phase 1: PLANNING     → Owner: Orchestrator        → Gate 1
Phase 2: SOLUTIONING  → Owner: Architect           → Gate 2
Phase 3: IMPLEMENTATION → Owner: Coder             → Gate 3
Phase 4: REVIEW       → Owner: QA                  → Gate 4
Phase 5: DEPLOY       → Owner: DevOps              → Gate 5
Phase 6: OPERATE      → Owner: Operator            → Gate 6
```

## Quick Flow (Small Tasks)

Untuk tasks yang tidak membutuhkan full 7-phase workflow:

```
Quick Flow: Intent → Plan → Implement → Review → Done
```

**When to use:**
- Bug fixes dengan scope jelas
- Simple features (< 15 stories)
- Refactoring dengan boundaries jelas
- Documentation updates

---

## Phase 0: Discovery & Alignment

**Goal:** Understand the problem, align intent, build shared vocabulary.

**Owner:** Intake (intake) + Orchestrator (orchestration)

**Activities:**
1. **Grilling session** — Interview user tentang apa yang diinginkan (MattPocock pattern)
2. **Build shared language** — CONTEXT.md glossary (MattPocock pattern)
3. **Domain research** — Researcher background research
4. **Product brief** — Structured task brief

**Outputs:**
- `docs/intake/task-brief-{id}.md`
- `CONTEXT.md` (if new project)

**Gate 0 Checklist:**
- [ ] Problem is clear
- [ ] Goal is clear
- [ ] Scope is bounded
- [ ] Out-of-scope is listed
- [ ] Assumptions are listed
- [ ] Success criteria are defined

**Decision:** PASS / CONCERNS / FAIL

---

## Phase 1: Planning

**Goal:** Define what to build, break work into stories.

**Owner:** Orchestrator

**Support:** Researcher, Documenter

**Activities:**
1. Create PRD from brief (BMAD pattern)
2. Define user stories with acceptance criteria
3. Define non-functional requirements
4. Create CONTEXT.md if not exists
5. Break into epics and stories

**Outputs:**
- `docs/PRD.md`
- `docs/epics/*.md`
- Updated `CONTEXT.md`

**Gate 1 Checklist:**
- [ ] PRD exists
- [ ] Stories exist with acceptance criteria
- [ ] NFRs exist
- [ ] Success metrics are measurable
- [ ] Scope is stable

**Decision:** PASS / CONCERNS / FAIL

---

## Phase 2: Solutioning

**Goal:** Define how to build it, break work into implementable units.

**Owner:** Architect

**Support:** Orchestrator, Researcher, Security

**Activities:**
1. Create architecture document (BMAD spine pattern)
2. Define components and boundaries
3. Define data flow and API contracts
4. Create ADRs for major decisions
5. Run implementation readiness check (BMAD pattern)
6. Create epics and stories from architecture

**Outputs:**
- `docs/architecture.md` (or `ARCHITECTURE-SPINE.md`)
- `docs/ADRs/*.md`
- `docs/readiness-report.md`

**Gate 2 Checklist:**
- [ ] Architecture exists
- [ ] ADRs exist for major decisions
- [ ] Components are clear
- [ ] Boundaries are clear
- [ ] Security model is defined
- [ ] Deployment model is defined
- [ ] Implementation readiness is PASS

**Decision:** PASS / CONCERNS / FAIL

---

## Phase 3: Implementation

**Goal:** Build story by story, with discipline.

**Owner:** Coder

**Support:** Architect (architecture clarification), Researcher (research), Security (security-sensitive code)

**Activities (per story):**
1. Pick next story from sprint backlog
2. Git worktree for isolation (Superpowers pattern)
3. Apply Ponytail ladder of laziness
4. Write tests FIRST — RED → GREEN → REFACTOR (TDD)
5. Implement minimum code to pass
6. Verify everything
7. Create atomic commit
8. Self-review against plan
9. Mark story done, pick next

**Outputs:**
- Source code
- Tests
- Implementation summary
- Risk notes

**Gate 3 Checklist:**
- [ ] All stories done
- [ ] All tests pass
- [ ] No architecture violations
- [ ] No hardcoded secrets
- [ ] No unapproved dependencies

**Decision:** PASS / CONCERNS / FAIL

---

## Phase 4: Review

**Goal:** Independent validation and quality assurance.

**Owner:** QA

**Support:** Security (security), Documenter (docs)

**Activities:**
1. Code review (two-stage: spec compliance → code quality)
2. Security review (Security agent)
3. Test coverage validation
4. Architecture compliance check
5. Documentation review (Documenter)
6. Fix review issues

**Outputs:**
- `docs/reviews/code-review-{id}.md`
- `docs/security/security-review-{id}.md`
- Updated code (fixes)

**Gate 4 Checklist:**
- [ ] Code review passed
- [ ] Security review passed
- [ ] All tests still pass
- [ ] Documentation updated
- [ ] No blocking issues

**Decision:** PASS / CONCERNS / FAIL

---

## Phase 5: Deploy

**Goal:** Deploy with approval, verify, rollback plan ready.

**Owner:** DevOps

**Support:** Security (security), Operator (monitoring)

**Activities:**
1. Prepare deployment plan
2. Validate environment
3. Deploy to staging
4. Run smoke tests
5. Prepare rollback plan
6. Request human approval for production
7. Deploy to production (if approved)
8. Verify health

**Outputs:**
- `docs/deployment.md`
- `docs/runbook.md`
- `docs/commissioning-report.md`

**Gate 5 Checklist:**
- [ ] Staging deployment successful
- [ ] Smoke tests pass
- [ ] Rollback plan exists
- [ ] Human approval received
- [ ] Production deployment verified

**Decision:** PASS / CONCERNS / FAIL

---

## Phase 6: Operate

**Goal:** Monitor, maintain, and learn.

**Owner:** Operator

**Support:** DevOps, Security

**Activities:**
1. Monitor service health
2. Track metrics and logs
3. Detect and respond to incidents
4. Update runbook with lessons learned
5. Feed learnings back to framework

**Outputs:**
- `docs/ops/health-report.md`
- `docs/ops/incident-report.md`
- Updated runbook

---

## Gate Decision Protocol

For each gate:

1. Owner generates gate document: `docs/gates/gate-{N}-{task-id}.md`
2. Owner completes checklist
3. Owner recommends decision (PASS/CONCERNS/FAIL)
4. Orchestrator reviews and confirms
5. Human approval required for Gate 2, 4, 5
6. Decision logged in decision log
7. On PASS: proceed to next phase
8. On CONCERNS: proceed with monitoring plan
9. On FAIL: remediate, do not proceed
