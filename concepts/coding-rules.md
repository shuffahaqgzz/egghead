---
title: Coding Rules
created: 2026-06-20
updated: 2026-06-20
type: concept
tags: [best-practice, implementation, code-pattern]
sources: [_archive/AGENTS.md, _archive/CODE_REVIEW.md, _archive/ADR-001-FRAMEWORK.md]
confidence: high
---

# Coding Rules

Coding rules define the standards for implementation (EDISON) and review (YORK) within the [[gate-system|phase-gated workflow]]. Rules enforce test-driven development, minimal scope, and architecture compliance.

## EDISON — Implementation Rules

| Rule | Rationale |
|------|-----------|
| Write tests first | TDD ensures correctness before implementation |
| Make small changes | Reduce blast radius, ease review |
| Verify after each change | Catch regressions immediately |
| Keep code runnable | Never leave broken states |
| Prefer simple code | Reduce maintenance burden |
| Avoid premature abstraction | YAGNI — add complexity only when needed |
| Do not add dependencies without approval | Every dep is a supply chain risk |
| Do not modify unrelated files | Scope discipline |
| Do not hardcode secrets | [[security-model\|Security]] requirement |
| Do not bypass architecture boundaries | ADR decisions are binding |

## YORK — Review Checklist

Every code review must verify:

1. **Correctness** — Logic matches acceptance criteria
2. **Tests** — Coverage adequate, integration tests present
3. **Edge cases** — Error paths handled
4. **Security** — No secrets, hardened config
5. **Architecture compliance** — Follows ADRs and component boundaries
6. **Naming consistency** — snake_case, clear names
7. **Unnecessary complexity** — No over-engineering
8. **Documentation impact** — Runbook/docs updated if needed
9. **Observability impact** — Health checks, logging adequate

### Review Verdicts

- **PASS** — No blocking findings
- **CONCERNS** — Non-blocking findings noted (NIT, MINOR)
- **FAIL** — Blocking issues must be resolved

## Code Review Example (hermes-pingback)

From [[code-review-wiki]]:
- 92 LOC server, zero external deps
- 9/9 tests pass in 1.17s (integration tests, real subprocess + HTTP)
- Ruff clean, snake_case consistent
- Non-blocking findings: misleading docstring, missing README

## Framework Selection Principle

From [[framework-selection-adr|ADR-001]]:
> Choose the simplest tool that works. stdlib over FastAPI when scope is trivially small.

Migration trigger: >3 endpoints, request body parsing, auth needed, or structured middleware needed.

## Related Concepts

- [[gate-system]] — Code review gate at Phase 4
- [[security-model]] — Security rules for implementation
- [[overall-architecture]] — Architecture compliance requirements
- [[framework-selection-adr]] — Technology decision records
