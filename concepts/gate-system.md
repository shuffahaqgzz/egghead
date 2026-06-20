---
title: Gate System
created: 2026-06-20
updated: 2026-06-20
type: concept
tags: [workflow, pipeline, state-machine, orchestration]
sources: [_archive/GATES.md, _archive/AGENTS.md]
confidence: high
---

# Gate System

The gate system enforces structured progression through the [[overall-architecture|workflow lifecycle]]. Each phase has a quality gate that must be evaluated before work advances. Gates prevent unreviewed code from reaching production and ensure all artifacts (PRD, architecture, tests, reviews) exist before deployment.

## Phase-to-Gate Mapping

| Phase | Name | Owner | Gate | Key Artifacts |
|-------|------|-------|------|---------------|
| 0 | Discovery | KUMACHII / STELLA | Gate 0 | Brief, assumptions, scope |
| 1 | Planning | STELLA | Gate 1 | [[prd-wiki\|PRD]], acceptance criteria |
| 2 | Solutioning | SHAKA | Gate 2 | [[overall-architecture\|Architecture]], [[framework-selection-adr\|ADRs]] |
| 3 | Implementation | EDISON | Gate 3 | Code, tests (TDD) |
| 4 | Review | YORK | Gate 4 | [[code-review-wiki\|Code review]], [[security-model\|security review]] |
| 5 | Deploy | LILITH | Gate 5 | Deployment, smoke tests |
| 6 | Operate | STUSSY | Gate 6 | [[deployment-pattern\|Runbook]], monitoring |

## Gate Decision Outcomes

| Outcome | Meaning | Action |
|---------|---------|--------|
| **PASS** | All criteria met | Proceed to next phase |
| **CONCERNS** | Minor issues noted | Proceed with monitoring; log concerns |
| **FAIL** | Blocking issues | Remediate before continuing; task stays in current phase |

## Gate Evaluation Checklist (Example: Gate 2 — Architecture)

From the hermes-pingback E2E lifecycle test:
- Architecture summary documented
- Components identified (HTTP server, tests, container, orchestration)
- ADRs accepted (ADR-001: stdlib over FastAPI)
- Reviewers: STELLA + human (Gilang)

## Gate Evidence Requirements

Each gate must include:
- **Reviewer identity** — Who approved
- **Checklist items** — Boolean pass/fail per criterion
- **Evidence** — Commands run, outputs captured (e.g., port scans, test results)
- **Resolved questions** — Open items from previous phases

## Enforcement Rules

From [[coding-rules|AGENTS.md]] phase rules:
1. Do not skip quality gates under any circumstance
2. Escalate risky ambiguity to STELLA before gate evaluation
3. Gate approvals for Gates 0/1/2/4/5 go to **Telegram** (user-facing)
4. Inter-agent gate communications stay on **Discord**

## Related Concepts

- [[overall-architecture]] — The workflow gates protect
- [[coding-rules]] — Standards enforced during implementation phase
- [[security-model]] — Security gate requirements (Gate 4)
- [[authority-hierarchy]] — Document authority during gate evaluation
