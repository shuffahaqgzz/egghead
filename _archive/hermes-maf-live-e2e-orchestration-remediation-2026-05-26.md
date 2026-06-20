# Hermes MAF Live E2E Orchestration Remediation - 2026-05-26

## Status

Post-run remediation plan after `MAF-AI-E2E-20260526-220239`.

The previous run validated provider loading, skill mapping, local artifact generation, and negative gate behavior. It failed the intended live orchestration contract. Do not treat that run as an accepted live E2E lifecycle test.

## Failed Live Contracts

| ID | Contract | Current Failure | Required Fix | Verification Evidence |
|---|---|---|---|---|
| `ORCH-001` | Handoff brief payload | Agents send long template-style handoff messages | Handoff messages must contain only the task brief or a concise task-brief pointer, with full detail stored in files | Captured outgoing Discord/agent message body is minimal and points to task brief |
| `ORCH-002` | Per-task thread lifecycle | Task brief does not create a new thread; work stays in channels or ad hoc messages | Enable or implement task-thread creation; all downstream agent work must occur in the task thread | Report records thread ID for kickoff and per-phase work; channel-only IDs are insufficient |
| `ORCH-003` | STELLA approval path | Gate approvals are not requested through Telegram | STELLA must send phase approval request to Telegram with task brief before every phase transition | Telegram message ID, approval response ID, phase, gate, and task ID are recorded |
| `ORCH-004` | Closeout chain | STELLA closeout does not prove report-back to KUMACHII and then User | STELLA must report completion to KUMACHII; KUMACHII must summarize to User | Report records STELLA -> KUMACHII message ID and KUMACHII -> User message ID |
| `ORCH-005` | Observer-only test execution | Operator nudged/drove agent sessions during run | Operator may only observe logs/files while run is active; allowed actions are initial kickoff and explicit approval replies | Audit log has no operator task-driving messages after kickoff except approval responses |

## Evidence Anchors

- `profiles/stella/SOUL.md` already requires Telegram for user-facing gate approvals and says gate approval prompts must not go to Discord.
- `profiles/stella/SOUL.md` already requires compact file-based handoff headers under 500 chars.
- `profiles/stella/SOUL.md` states Discord specialist channels are type 0 text channels and do not auto-create threads.
- `config.yaml` has `discord.auto_thread: true`, but the live test still did not capture thread IDs. This suggests the flag is insufficient for the intended per-task thread contract or does not apply to this routing path.

## Root Cause Investigation Targets

These are hypotheses to verify before code/config changes:

1. `discord-routing` or gateway send path may post to channels without explicit thread creation, despite `discord.auto_thread: true`.
2. Handoff template enforcement may exist in STELLA instructions but not be reliably followed or validated before send.
3. STELLA gate policy may be documented in `SOUL.md`, but the active runtime path may not enforce Telegram approval as a hard blocking state.
4. Closeout policy may end at STELLA artifact generation instead of enforcing STELLA -> KUMACHII -> User report-back.
5. The test procedure allowed manual operator nudge messages, which contaminated autonomous orchestration evidence.

## Required Code/Config Work Before Retest

1. Locate and fix handoff message generation so the outbound agent handoff contains only:
   - task ID
   - phase
   - target agent
   - one-line objective
   - task brief file path or thread link

2. Locate Discord routing/thread code and enforce one explicit thread strategy:
   - create a Discord thread programmatically on new task brief, or route tasks to a forum/thread-capable channel
   - do not rely on text-channel auto-thread assumptions
   - persist thread ID to task state
   - route all phase messages into that thread
   - fail gate if thread ID is missing

3. Locate STELLA gate transition code/profile instructions and enforce:
   - Telegram approval request before each phase
   - approval request includes task brief
   - phase remains blocked until approval response is recorded
   - report stores Telegram request and response IDs

4. Locate closeout code/profile instructions and enforce:
   - STELLA sends closeout to KUMACHII
   - KUMACHII summarizes final result to User
   - report stores both message IDs

5. Update live E2E procedure to observer-only mode:
   - no manual handoff/nudge after kickoff
   - no direct specialist prompting by operator
   - operator watches logs/files only
   - if agents stall, record stall and fail the run instead of intervening

## Retest Acceptance Criteria

A retest may only be marked PASS when all are true:

- Provider validation passes for all 10 agents or documented fallback is used within the 2-attempt limit.
- Skill mapping passes for all 10 agents.
- Kickoff creates a task thread and records thread ID.
- Every phase handoff happens inside the task thread.
- Every handoff outbound message is minimal task-brief form.
- STELLA requests every phase approval through Telegram and records Telegram request/response IDs.
- STELLA blocks phase progression without Telegram approval.
- STELLA reports final closeout to KUMACHII.
- KUMACHII reports final summary to User.
- Operator does not intervene while the run is active, except explicit approval responses.
- Negative gate tests still block/refuse/escalate correctly.
- No secrets are printed.
- No production/destructive actions are performed.

## Retest Rule

If any autonomous step stalls, the retest must fail with the last observed log/file evidence. Do not manually complete the phase on behalf of the agent.
