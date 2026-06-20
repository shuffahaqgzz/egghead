# Full E2E Lifecycle Test — Phase 0→6 through 10 agents

**Test ID:** `E2E-LIFECYCLE-001`
**Date:** 2026-05-16
**Owner:** STELLA (orchestrator)
**Goal:** Validate one task flowing through complete workflow lifecycle (Phase 0→6) across all 10 agents, with proper handoffs and gate enforcement.

---

## Test Task Selection

**Task:** Add a `/health` endpoint to a new tiny LAN service called `hermes-pingback`.

Why this task:
- Small enough to finish in one workflow cycle (~60 min agent time)
- Touches every agent role naturally (no skipped phases)
- Greenfield — no existing infra to corrupt
- Clear acceptance: `GET http://10.50.0.111:<PORT>/health` returns `{"status":"ok","version":"0.1.0"}`
- Failure-safe — if E2E breaks midway, we learn where; pingback service is throwaway

**Out of scope:** anything beyond basic health endpoint (no auth, no metrics, no CI).

---

## Expected Phase Flow

| Phase | Owner | Deliverable | Gate |
|---|---|---|---|
| 0 Discovery | KUMACHII (intake) → STELLA | `docs/intake/task-brief-E2E-LIFECYCLE-001.md` | Gate 0 |
| 1 Planning | STELLA | `docs/prd-E2E-LIFECYCLE-001.md` with acceptance criteria | Gate 1 |
| 2 Solutioning | SHAKA + ATLAS (security review) | `docs/arch/arch-pingback.md` + ADR if needed | Gate 2 (HUMAN) |
| 3 Implementation | EDISON (TDD: RED→GREEN→REFACTOR) | Code + tests in `/notflix/apps/hermes-pingback/` | Gate 3 |
| 4 Review | YORK (8-dim) + ATLAS (security) | review reports | Gate 4 (HUMAN) |
| 5 Deploy | LILITH | Service running on `/notflix/apps/hermes-pingback/`, healthcheck passing | Gate 5 (HUMAN) |
| 6 Operate | STUSSY | Health watchdog cronjob + runbook | Gate 6 |

PYTHAGORAS, BONNEY participate as needed:
- PYTHAGORAS: Phase 2 — research best practice for tiny health-only services in Python (FastAPI vs http.server)
- BONNEY: Phase 6 — write ops runbook + ingest task brief into RAG

---

## Kickoff Message (to KUMACHII)

```
yoo, ada task baru.

ID: E2E-LIFECYCLE-001
Goal: Bikin tiny LAN service `hermes-pingback` yang expose GET /health → {"status":"ok","version":"0.1.0"}

Constraints:
- Deploy ke /notflix/apps/hermes-pingback/ pakai docker compose
- Bind ke 10.50.0.111 dengan port baru (cek ketersediaan)
- Stack: Python (FastAPI atau stdlib http.server, terserah arsitek)
- LAN-only, traefik.enable=false
- Healthcheck: curl localhost:PORT/health → 200 + JSON valid

Workflow: Full v1.1.0 lifecycle Phase 0→6 via STELLA orchestration. Semua 10 agent harus participate sesuai role.

Forward ke STELLA untuk Phase 0 discovery + scope.
```

---

## Success Criteria

- [ ] Task brief landed di `docs/intake/task-brief-E2E-LIFECYCLE-001.md`
- [ ] Each phase has gate decision document (`docs/gates/gate-N-E2E-LIFECYCLE-001.md`)
- [ ] All 10 agents posted at least 1 substantive message related to this task
- [ ] EDISON commits with conventional format + TDD evidence
- [ ] YORK + ATLAS review reports both reach PASS or CONCERNS (no FAIL)
- [ ] LILITH deploy succeeds, healthcheck green
- [ ] STUSSY watchdog cronjob registered
- [ ] BONNEY runbook written
- [ ] Total duration ≤ 4 hours wall-clock from kickoff to operate

## Failure Modes to Watch

| Failure | Detection | Recovery |
|---|---|---|
| STELLA loops without delegating | No specialist agent posts within 15 min of Phase 1 start | Re-prompt STELLA explicitly with delegation matrix |
| Agent misses handoff | No follow-up post in target agent's channel | Re-post handoff in target channel |
| Gate skipped | Phase advances without `docs/gates/gate-N-*.md` | STELLA back-fills gate doc |
| ATLAS notification posted to own channel (regression) | Block notification not in #stella🤓 | Already fixed turn ini — should not happen |
| Cross-agent recall fails | Agent X cannot recall fact observed by Agent Y | Already fixed via Option A — verify works |

---

## Monitoring

Cronjob `e2e-lifecycle-watch` polls every 30 min:
1. Counts new messages per channel since last poll
2. Detects stalled phases (no activity > 60 min)
3. Reports gate doc presence
4. Discord-pushes summary to home channel

Manual checks:
- `ls /home/infra/projects/hermes-pingback/docs/` (created by agents)
- `docker ps | grep hermes-pingback` (Phase 5+)
- `curl http://10.50.0.111:<PORT>/health` (Phase 5+)

---

## Test Status

- [ ] Kickoff sent to KUMACHII
- [ ] Phase 0 — Discovery
- [ ] Phase 1 — Planning
- [ ] Phase 2 — Solutioning (HUMAN approval)
- [ ] Phase 3 — Implementation
- [ ] Phase 4 — Review (HUMAN approval)
- [ ] Phase 5 — Deploy (HUMAN approval)
- [ ] Phase 6 — Operate
- [ ] Post-test analysis report

## Post-test Analysis Template

After completion (or failure):
- What worked: <list>
- What broke: <list with evidence>
- Time per phase: <table>
- Gate enforcement: <assessment>
- Honcho cross-agent recall during E2E: <observations>
- Recommendations for v1.1.1 / v1.2.0: <list>
