# Smoke Test Plan — Installed Skills

> **For Hermes:** Execute this plan task-by-task. Each task is a test category.

**Goal:** Verify all installed skills are structurally valid (Phase A) and functionally work when used (Phase B).

**Architecture:** Phase A = structural validation script. Phase B = 24 functional tests via delegate_task subagents.

**Scope:** `~/.hermes/skills/multi-agent/` + `~/.hermes/skills/bmad/`

---

# Phase A — Structural Tests

## Task 1: Structural Validation

**Objective:** Verify every SKILL.md has valid YAML frontmatter and required fields.

**What it checks:**
- SKILL.md exists in each skill directory
- YAML frontmatter exists (starts with `---`)
- Required fields: `name`, `description`
- No UTF-8 BOM, file is non-empty

**How:** Python script via `execute_code`.

---

## Task 2: Reference Integrity

**Objective:** Verify intra-skill file references resolve.

**What it checks:**
- `[text](relative/path)` links → target file exists
- Skip http/https and `[[wikilinks]]`
- `skill_view(name='X')` → X exists as a skill

**How:** Regex + `os.path.exists()`.

---

## Task 3: Hermes Registration

**Objective:** Verify all skills on disk appear in `hermes skills list`.

**How:** Terminal command + set comparison.

---

## Task 4: Content Sanity

**Objective:** Flag suspicious content patterns.

**What it checks:**
- SKILL.md < 20 lines (suspiciously short)
- Truncation markers at end of file
- Unconverted `/slash-command` outside code blocks
- Unclosed code blocks (odd ``` count)

**How:** Python regex + line counting.

---

## Task 5: Loadability Test (Sample 15)

**Objective:** Verify `skill_view()` loads skills without error.

**Sample:** `systematic-debugging`, `test-driven-development`, `triage`, `brainstorming`, `grill-me`, `handoffs`, `tdd`, `verification-before-completion`, `subagent-driven-development`, `architect`, `bmad-brainstorming`, `bmad-architecture`, `bmad-code-review`, `bmad-create-story`, `bmad-spec`

---

# Phase B — Functional Tests

**Method:** Spawn subagents via `delegate_task`. Each loads one skill, receives a minimal test prompt, executes. Parent verifies output contains expected patterns.

**Execution:** Run in batches of 3 parallel subagents (8 batches total).

**Pass criteria:** Output contains key structural patterns (sections, keywords, format). Not exact match — behavioral verification.

---

## Workflow Skills (7 tests)

### B.1: grill — Intent Alignment

**Skill:** `grill`
**Prompt:** "I want to add a login page to my app."
**Expected:** Structured task brief with sections — Goal, Scope, Constraints, Acceptance Criteria (or equivalent structured breakdown).
**Pass if:** Output has ≥ 3 distinct sections with bullet points. Agent asks clarifying questions before coding.

### B.2: plan — PRD Creation

**Skill:** `plan`
**Prompt:** "Write a plan for adding user authentication to a Flask app. Support email/password login and Google OAuth."
**Expected:** Markdown plan with — Goal, Architecture/Approach, Step-by-step tasks, Files to change, Verification steps.
**Pass if:** Output is structured markdown with ≥ 4 sections and specific file paths or task descriptions.

### B.3: architect — Architecture + ADR

**Skill:** `architect`
**Prompt:** "Design the architecture for a real-time notification system in a Django app. Must support 10k concurrent users."
**Expected:** Component list, interface definitions, data flow, at least one ADR with Context/Decision/Consequences.
**Pass if:** Output has components table or list, and an ADR-formatted decision record.

### B.4: implement — TDD Workflow

**Skill:** `implement`
**Prompt:** "Implement a Python function `is_palindrome(s: str) -> bool`."
**Expected:** Test code appears BEFORE implementation. Red-green-refactor cycle demonstrated.
**Pass if:** `def test_` or `assert` appears before the implementation function definition.

### B.5: review — Code Review

**Skill:** `review`
**Prompt:** "Review this code:\n```python\ndef add(a, b):\n    return a + b\n```\nThis is used in a financial calculator."
**Expected:** Two-stage review (spec compliance + code quality). Severity levels (critical/important/minor). Specific findings.
**Pass if:** Output has severity labels and ≥ 2 specific findings or recommendations.

### B.6: deploy — Deployment Workflow

**Skill:** `deploy`
**Prompt:** "We have a Flask app ready for production. Database migrations are done. Staging tests pass. Prepare deployment."
**Expected:** Pre-deploy checklist, staging steps, rollback preparation, approval request format.
**Pass if:** Output has checklist items and mentions staging/rollback/approval.

### B.7: quick-flow — Abbreviated Workflow

**Skill:** `quick-flow`
**Prompt:** "Fix the typo in README.md where 'recieve' should be 'receive'."
**Expected:** Short, direct response. No ceremony. Addresses the fix immediately.
**Pass if:** Output is < 300 words and directly addresses the fix without elaborate planning.

---

## Discipline Skills (8 tests)

### B.8: tdd — Test-First Discipline

**Skill:** `tdd`
**Prompt:** "Write a Python function `factorial(n: int) -> int` that returns the factorial of n."
**Expected:** Test written first (RED), then implementation (GREEN), then refactor. Anti-pattern: horizontal slicing avoided.
**Pass if:** Test/assert statement appears before implementation. Red-green-refactor mentioned or demonstrated.

### B.9: ponytail — Ladder of Laziness

**Skill:** `ponytail`
**Prompt:** "Add a cache for these API responses."
**Expected:** References stdlib (e.g., `functools.lru_cache`), questions whether custom cache is needed, applies YAGNI.
**Pass if:** Mentions stdlib, YAGNI, or questions the need for custom implementation. Output follows `[code] → skipped: [X]` pattern.

### B.10: diagnosing-bugs — Bug Diagnosis

**Skill:** `diagnosing-bugs`
**Prompt:** "My function returns None instead of the expected list. Here's the code:\n```python\ndef get_items(data):\n    for item in data:\n        if item.active:\n            return item.list\n```"
**Expected:** Identifies the bug (returns on first match instead of collecting all), explains root cause, suggests fix.
**Pass if:** Output identifies the specific bug (early return / missing collection) and explains why it's wrong.

### B.11: domain-modeling — Glossary

**Skill:** `domain-modeling`
**Prompt:** "We're building an e-commerce platform. Terms like 'order', 'cart', 'checkout', 'payment' are used. Build a glossary."
**Expected:** Structured glossary with term definitions, distinctions between similar terms (order vs cart vs checkout), edge cases.
**Pass if:** Output has a glossary table or definition list with ≥ 4 terms defined.

### B.12: codebase-design — Module Analysis

**Skill:** `codebase-design`
**Prompt:** "Analyze this module structure:\n- `auth/login.py` — handles login\n- `auth/logout.py` — handles logout\n- `auth/session.py` — manages sessions\n- `auth/validate.py` — validates tokens\nAll files import each other circularly."
**Expected:** Identifies circular dependency, suggests module boundaries, proposes interface redesign.
**Pass if:** Output mentions circular dependency and proposes a structural solution.

### B.13: systematic-debugging — Root Cause

**Skill:** `systematic-debugging`
**Prompt:** "My Python script crashes with `KeyError: 'user_id'` when processing empty CSV files. It works fine with non-empty CSVs."
**Expected:** 4-phase process: reproduce → read error → isolate → hypothesize. Does NOT jump to fix immediately. Asks about reproduction steps.
**Pass if:** Output has distinct phases/steps. Forms hypothesis before suggesting fix.

### B.14: verification-before-completion — Pre-Claim

**Skill:** `verification-before-completion`
**Prompt:** "I just finished implementing the auth module with JWT tokens. Tests pass. Code review done. Verify it's complete."
**Expected:** Runs through checklist (tests, security, scope, docs, safety) before claiming done. Does not just say "looks good."
**Pass if:** Output has ≥ 5 checklist items with status (PASS/FAIL or ✓/✗ per item).

### B.15: curator — Conflict Resolution

**Skill:** `curator`
**Prompt:** "Skill A says 'always write tests before code'. Skill B says 'ship first, test later'. Which one wins for a hotfix?"
**Expected:** Identifies the conflict, applies resolution rules (task complexity, explicit override, domain authority), gives clear verdict.
**Pass if:** Output identifies both skills, applies a resolution rule, and gives a clear recommendation.

---

## Agent Skills (6 tests)

### B.16: handoffs — Agent Transfer

**Skill:** `handoffs`
**Prompt:** "We were building a login feature. Completed: database schema, user model, JWT auth. Remaining: OAuth integration, rate limiting, tests. Key decision: JWT over sessions."
**Expected:** Structured handoff doc with — Goal, Status table, Decisions, Artifacts, Next Action, Suggested Skills.
**Pass if:** Output has ≥ 5 structured sections matching handoff template.

### B.17: security-review — Threat Assessment

**Skill:** `security-review`
**Prompt:** "Review this endpoint for security:\n```python\n@app.route('/login', methods=['POST'])\ndef login():\n    username = request.form['username']\n    password = request.form['password']\n    user = db.query(f\"SELECT * FROM users WHERE name='{username}' AND pass='{password}'\")\n    if user: return generate_token(user)\n    return 'Invalid', 401\n```"
**Expected:** Identifies SQL injection, missing input validation, plaintext password comparison, no rate limiting.
**Pass if:** Output has severity-rated findings (critical/high/medium/low) and identifies SQL injection.

### B.18: qa-review — Quality Assurance

**Skill:** `qa-review`
**Prompt:** "Review this test suite for a login function:\n```python\ndef test_login_works():\n    result = login('admin', 'password123')\n    assert result == True\n```"
**Expected:** Identifies issues: only happy path tested, no edge cases, hardcoded credentials, no negative tests, tests implementation not behavior.
**Pass if:** Output lists ≥ 3 quality issues with recommendations.

### B.19: deployment-checklist — Pre-Deploy

**Skill:** `deployment-checklist`
**Prompt:** "We're about to deploy a Django app to production. PostgreSQL database, Redis cache, deployed via Docker. What's the checklist?"
**Expected:** Environment, dependencies, configuration, rollback, communication sections with specific checklist items.
**Pass if:** Output has ≥ 4 categories with checklist items.

### B.20: monitoring-setup — Observability

**Skill:** `monitoring-setup`
**Prompt:** "Set up monitoring for a new REST API service. It handles payment processing."
**Expected:** Health metrics, application metrics, alerting rules, logging config, dashboard suggestions. Payment-specific monitoring (error rates, latency).
**Pass if:** Output has ≥ 3 monitoring categories and mentions payment-specific concerns.

### B.21: docs-generation — Documentation

**Skill:** `docs-generation`
**Prompt:** "Generate API documentation for this endpoint:\n```python\n@app.route('/api/users', methods=['GET'])\ndef get_users():\n    page = request.args.get('page', 1, type=int)\n    users = User.query.paginate(page=page, per_page=20)\n    return jsonify([u.to_dict() for u in users])\n```"
**Expected:** Structured API docs with — endpoint, method, parameters, response format, pagination details.
**Pass if:** Output has endpoint, method, parameters table, and response example.

---

## Optimization Skills (3 tests)

### B.22: caveman-output — Terse Response

**Skill:** `caveman-output`
**Prompt:** "Explain what this code does:\n```python\ndef fib(n):\n    a, b = 0, 1\n    for _ in range(n):\n        a, b = b, a + b\n    return a\n```"
**Expected:** Minimal response — code identified as Fibonacci, explanation in ≤ 3 lines. No essays.
**Pass if:** Output is < 100 words and correctly identifies the function.

### B.23: code-review-graph — Blast Radius

**Skill:** `code-review-graph`
**Prompt:** "I'm changing the `get_user(id)` function in `utils/database.py`. This is a shared utility used by auth, payments, and notifications. Analyze the blast radius."
**Expected:** Identifies affected modules (auth, payments, notifications), recommends caution, suggests testing strategy.
**Pass if:** Output lists ≥ 3 affected areas and recommends specific verification steps.

### B.24: smart-zone — Context Optimization

**Skill:** `smart-zone`
**Prompt:** "I have a 5000-line Python file. I need to fix a bug in the payment processing section around line 3200. What's the most token-efficient way to approach this?"
**Expected:** Recommends targeted reading (offset/limit), not loading entire file. Suggests search-first approach. Mentions Dumb Zone warning.
**Pass if:** Output recommends targeted reading with specific line ranges or search strategy.

---

# Expected Output

```
=== SMOKE TEST RESULTS ===

--- Phase A: Structural ---
Task 1 - Structural:    89/89 PASS (or X FAIL)
Task 2 - References:    X broken links found
Task 3 - Registration:  89/89 registered (or X mismatch)
Task 4 - Content:       X warnings, 0 errors
Task 5 - Loadability:   15/15 loaded

--- Phase B: Functional ---
B.1  grill:               PASS / FAIL
B.2  plan:                PASS / FAIL
B.3  architect:           PASS / FAIL
B.4  implement:           PASS / FAIL
B.5  review:              PASS / FAIL
B.6  deploy:              PASS / FAIL
B.7  quick-flow:          PASS / FAIL
B.8  tdd:                 PASS / FAIL
B.9  ponytail:            PASS / FAIL
B.10 diagnosing-bugs:     PASS / FAIL
B.11 domain-modeling:     PASS / FAIL
B.12 codebase-design:     PASS / FAIL
B.13 systematic-debugging: PASS / FAIL
B.14 verification:        PASS / FAIL
B.15 curator:             PASS / FAIL
B.16 handoffs:            PASS / FAIL
B.17 security-review:     PASS / FAIL
B.18 qa-review:           PASS / FAIL
B.19 deployment-checklist: PASS / FAIL
B.20 monitoring-setup:    PASS / FAIL
B.21 docs-generation:     PASS / FAIL
B.22 caveman-output:      PASS / FAIL
B.23 code-review-graph:   PASS / FAIL
B.24 smart-zone:          PASS / FAIL

OVERALL: PASS / FAIL
```

## Risks

| Risk | Mitigation |
|------|-----------|
| 24 functional tests = ~48k tokens | Batch 3 parallel subagents per call |
| BMAD skills expect BMAD config | Test with minimal prompts, accept partial compliance |
| Agent may not follow skill perfectly | Pass if output has key patterns, not exact match |
| Optimization skills are model-invoked | Test by giving task that triggers the discipline |
| Some skills need project context | Provide minimal context in prompt |
| Curator test needs conflict scenario | Pre-define the conflict in prompt |

## Files

- Plan: `~/egghead-wiki/plans/2026-06-22-smoke-test-skills.md` (this file)
- Script: executed via `execute_code` (structural) + `delegate_task` (functional)
- Results: appended to this plan file after execution
