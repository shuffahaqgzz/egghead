---
title: "Code Review Workflow"
created: 2026-06-20
updated: 2026-06-20
type: concept
tags: [review, quality, code-review, workflow]
sources: []
confidence: high
---

# Code Review Workflow

Two-stage code review process untuk [[egghead-framework]].

## Two-Stage Review (Superpowers Pattern)

### Stage 1: Spec Compliance
Does the implementation match the story/PRD?
- Acceptance criteria met?
- Architecture followed?
- Scope respected?

### Stage 2: Code Quality
Is the code correct, maintainable, and safe?
- Correctness
- Test coverage
- Edge cases
- Security
- Naming consistency
- Unnecessary complexity

## Review Roles

| Role | Responsibility |
|------|---------------|
| **QA** | Primary reviewer, two-stage review |
| **Security** | Security-specific review (if security-sensitive code) |
| **Orchestrator** | Final gate decision |

## Review Output

```markdown
# Code Review: [Task/Feature ID]

## Verdict: APPROVED / CHANGES REQUESTED / BLOCKED

## Stage 1: Spec Compliance
- [ ] Acceptance criteria met
- [ ] Architecture followed
- [ ] Scope respected

## Stage 2: Code Quality
- [ ] Correctness
- [ ] Tests comprehensive
- [ ] Edge cases handled
- [ ] No security concerns
- [ ] Naming clear
- [ ] No unnecessary complexity

## Findings
### Critical
- [...]
### Important
- [...]
### Suggestions
- [...]
```

## See Also

- [[workflow-lifecycle]] — Phase 4 Review details
- [[skill-architecture]] — Review skills
