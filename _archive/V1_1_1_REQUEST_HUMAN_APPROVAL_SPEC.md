# v1.1.1 Implementation Spec: `request_human_approval` Tool

**Status:** SPEC — not yet implemented
**Effort estimate:** 2.5-3.5 hours senior engineering + testing
**Last updated:** 2026-05-16

## Problem Statement

Current Gate 2/4/5 approvals require human (Gilang) to type text replies (`approve` / `concerns: X` / `reject: Y`) in Telegram → Hermes orchestrator forwards to STELLA Discord channel. Adds 30-90s latency per gate, prone to manual nudge errors, and feels clunky on mobile.

Goal: STELLA calls `request_human_approval` tool → Telegram message arrives with PASS / CONCERN / FAIL inline buttons → user taps → choice routed back to STELLA channel automatically.

## Existing Infrastructure (Reusable)

1. **`send_exec_approval`** in `gateway/platforms/telegram.py:1708` — already has the InlineKeyboardButton + callback pattern with `ea:choice:id` callback_data.
2. **`_handle_callback_query`** in `gateway/platforms/telegram.py:2131` — dispatches callback button clicks, has authorization check (`_is_callback_user_authorized`).
3. **`_approval_state`** dict at `telegram.py:318` — maps approval_id → session_key for state lookup.
4. **`tools/approval.py`** — has `_gateway_queues`, `register_gateway_notify`, `resolve_gateway_approval` for blocking approval lifecycle (used for shell command approval).
5. **`gateway.mirror.mirror_to_session`** — can inject text into a target session as if it were inbound.

## Design Decision

**Non-blocking pattern, NOT blocking.**

Why: blocking approval (waiting on `event.set()` like `send_exec_approval`) requires the agent loop to suspend. STELLA should NOT suspend — she has other work, other tasks, other channels. She's an orchestrator.

Pattern: STELLA fires "I need approval" + button choices → returns immediately ("approval requested"). User taps button → callback handler injects `"PASS"` (or chosen value) as a text reply into STELLA's channel via `mirror_to_session` or by calling the inbound message handler directly. STELLA picks up the reply on her next message round-trip naturally.

## Implementation Plan

### Step 1 — New tool `request_human_approval`

File: `tools/request_human_approval.py` (new)

```python
TOOL_SCHEMA = {
    "name": "request_human_approval",
    "description": (
        "Send an interactive approval prompt to the human via Telegram with "
        "buttons. The user's choice is forwarded back to your channel as a "
        "text message — pick it up on your next turn. "
        "NON-BLOCKING: this returns immediately after sending the prompt."
    ),
    "parameters": {
        "type": "object",
        "properties": {
            "title": {"type": "string", "description": "Short title (e.g., 'Gate 4 Approval — E2E-001')"},
            "summary": {"type": "string", "description": "Multi-line markdown explaining what's being approved"},
            "choices": {
                "type": "array",
                "description": "Button labels (max 6). E.g., ['PASS', 'CONCERN', 'FAIL']",
                "items": {"type": "string"},
                "minItems": 1,
                "maxItems": 6
            },
            "forward_target": {
                "type": "string",
                "description": "Where to forward user's choice. Format 'platform:chat_id'. Example 'discord:1502667343279423619'"
            },
            "telegram_target": {
                "type": "string",
                "description": "Optional. Telegram chat to send buttons (default: home channel)"
            }
        },
        "required": ["title", "summary", "choices", "forward_target"]
    }
}

def request_human_approval_tool(args, **kw):
    title = args["title"]
    summary = args["summary"]
    choices = args["choices"]
    forward_target = args["forward_target"]
    telegram_target = args.get("telegram_target", "telegram")  # home channel
    
    # Generate unique approval_id
    approval_id = uuid.uuid4().hex[:8]
    
    # Store mapping: approval_id → forward_target
    from tools.user_approval import register_user_approval
    register_user_approval(approval_id, forward_target, title)
    
    # Send via Telegram adapter primitive
    from model_tools import _run_async
    from gateway.platforms.telegram import TelegramAdapter
    # Get telegram adapter from running gateway
    # ... (look up via gateway registry)
    result = _run_async(adapter.send_human_approval_buttons(
        chat_id=telegram_chat_id,
        title=title,
        summary=summary,
        choices=choices,
        approval_id=approval_id,
    ))
    return json.dumps({
        "success": True,
        "approval_id": approval_id,
        "message": "Approval prompt sent. User choice will arrive in your channel."
    })
```

Effort: 30 min.

### Step 2 — Telegram adapter primitive `send_human_approval_buttons`

File: `gateway/platforms/telegram.py` — add method ~line 1780 (after `send_exec_approval`)

```python
async def send_human_approval_buttons(
    self, chat_id: str, title: str, summary: str,
    choices: List[str], approval_id: str,
    metadata: Optional[Dict[str, Any]] = None,
) -> SendResult:
    """Send inline-keyboard human approval prompt (Gate-style PASS/CONCERN/FAIL).
    
    Different from send_exec_approval: NON-BLOCKING. Choice is delivered via
    message injection into forward_target, not via resolve_gateway_approval.
    """
    if not self._bot:
        return SendResult(success=False, error="Not connected")
    try:
        text = f"<b>{_html.escape(title)}</b>\n\n{_html.escape(summary)[:3500]}"
        # 3 buttons per row max for mobile UX
        rows = []
        for i in range(0, len(choices), 3):
            row = [
                InlineKeyboardButton(
                    text=choice,
                    callback_data=f"ua:{approval_id}:{idx + i}"
                )
                for idx, choice in enumerate(choices[i:i + 3])
            ]
            rows.append(row)
        keyboard = InlineKeyboardMarkup(rows)
        # ... (send_message with reply_markup, similar to send_exec_approval)
```

Effort: 30 min.

### Step 3 — Callback handler dispatch

File: `gateway/platforms/telegram.py` — extend `_handle_callback_query` ~line 2153 (insert after `ea:` handler).

```python
# --- User approval callbacks (ua:approval_id:choice_idx) ---
if data.startswith("ua:"):
    parts = data.split(":", 2)
    if len(parts) == 3:
        approval_id = parts[1]
        try:
            choice_idx = int(parts[2])
        except ValueError:
            await query.answer(text="Invalid choice.")
            return
        # Authorization check (same as ea:)
        # ...
        from tools.user_approval import resolve_user_approval, get_user_approval
        approval = get_user_approval(approval_id)
        if not approval:
            await query.answer(text="This approval has expired or was already resolved.")
            return
        choice = approval["choices"][choice_idx]  # the string
        forward_target = approval["forward_target"]
        # Edit message to lock buttons
        await query.edit_message_text(
            text=f"<b>{approval['title']}</b>\n\n✅ Resolved: <b>{choice}</b> by {user_display}",
            parse_mode=ParseMode.HTML, reply_markup=None,
        )
        # Forward choice as a text message to forward_target
        from tools.send_message_tool import _handle_send
        forward_result = _handle_send({
            "target": forward_target,
            "message": f"[Gate Approval] {approval['title']}: **{choice}** (by {user_display} via Telegram)"
        })
        # Resolve cleanup
        resolve_user_approval(approval_id)
    return
```

Effort: 30 min.

### Step 4 — Approval state module `tools/user_approval.py`

```python
"""User approval state — non-blocking gate approval queue."""

import threading
import time
from typing import Dict, Optional

_lock = threading.RLock()
_user_approvals: Dict[str, Dict] = {}  # approval_id → {"title", "choices", "forward_target", "created_at"}

# TTL: 24 hours
_TTL = 24 * 3600

def register_user_approval(approval_id: str, forward_target: str, 
                            title: str, choices: list) -> None:
    with _lock:
        _user_approvals[approval_id] = {
            "title": title,
            "choices": choices,
            "forward_target": forward_target,
            "created_at": time.time()
        }
        _gc()

def get_user_approval(approval_id: str) -> Optional[Dict]:
    with _lock:
        return _user_approvals.get(approval_id)

def resolve_user_approval(approval_id: str) -> bool:
    with _lock:
        return _user_approvals.pop(approval_id, None) is not None

def _gc():
    """Remove approvals older than TTL."""
    cutoff = time.time() - _TTL
    expired = [k for k, v in _user_approvals.items() if v["created_at"] < cutoff]
    for k in expired:
        _user_approvals.pop(k, None)
```

Effort: 15 min.

### Step 5 — Tool registration

File: `tools/registry.py` (or wherever `_HERMES_CORE_TOOLS` is) — add import + registration of `request_human_approval`.

Effort: 10 min.

### Step 6 — STELLA SOUL.md hook

File: `~/.hermes/profiles/stella/SOUL.md`

Add section:
```markdown
## Gate 2/4/5 Human Approval (button-based)

When you reach a gate that requires human approval (Gate 2 architecture, Gate 4 review, Gate 5 deploy), call:

```
request_human_approval(
  title="Gate 4 — <Task ID>",
  summary="""**Task:** <Task ID>
  **Phase 4 Review verdicts:**
  - YORK: <verdict>
  - ATLAS: <verdict>
  
  See `<doc paths>`.""",
  choices=["PASS", "CONCERN", "FAIL"],
  forward_target="discord:1502667343279423619"  # your own channel
)
```

The user's choice arrives in your channel as a text message tagged `[Gate Approval]`. Pick it up next turn and act:
- "PASS" → advance to next phase
- "CONCERN: <X>" → advance with monitoring item X
- "FAIL: <reason>" → return to current phase with remediation
```

Effort: 15 min.

### Step 7 — Tests

`tests/test_request_human_approval.py`:
- Schema validation
- Approval registration + lookup
- TTL expiry
- Resolution flow
- Forward to mock channel
- Authorization check passes/fails

Effort: 45 min.

### Step 8 — End-to-end smoke test

1. Restart all gateways
2. CLI test: `hermes chat -q "Use request_human_approval to ask Gilang via Telegram if I should proceed"` 
3. Verify button arrives, click PASS, verify text "[Gate Approval] ... PASS" lands at the configured forward_target
4. Run E2E with new STELLA workflow
5. Time gate latency: should drop from ~30-90s text-typing to ~5-10s button-tap

Effort: 30 min.

## Total: ~3.5 hours

| Step | Effort |
|---|---|
| 1. New tool | 30m |
| 2. Telegram primitive | 30m |
| 3. Callback handler | 30m |
| 4. State module | 15m |
| 5. Tool registration | 10m |
| 6. STELLA SOUL.md | 15m |
| 7. Tests | 45m |
| 8. E2E smoke | 30m |
| **Total** | **~3.5h** |

## Why Not Blocking Pattern (`send_exec_approval` Style)?

Blocking approval requires:
- Agent thread to suspend on `Event.wait()`
- Gateway timeout policies must permit long waits (currently 1800s for STELLA, fine)
- Risk of deadlock if user never responds
- STELLA cannot service other tasks/channels while waiting

For shell-command approval (use case of `send_exec_approval`), blocking is correct: the agent literally cannot proceed without the approval. For gate approval, STELLA can advance other tasks, monitor other agents, do other orchestration work — non-blocking is strictly better.

## Failure Modes & Mitigations

| Failure | Mitigation |
|---|---|
| User never clicks button | TTL 24h cleanup; STELLA's task stays "PENDING APPROVAL" until choice or manual override via text |
| User clicks but forward fails (Discord down) | Retry forward; on persistent fail, log error and edit Telegram message to "RESOLVED but FORWARD FAILED — copy choice manually to STELLA channel" |
| Multiple gates pending simultaneously | Each has unique approval_id; user can resolve independently |
| Wrong user clicks (auth bypass) | `_is_callback_user_authorized` already enforces |
| User picks wrong choice | Add "Cancel" button (7th choice) that re-opens prompt; or text reply override |

## Acceptance Criteria

- [ ] Button arrives at Telegram home channel within 5s of `request_human_approval` call
- [ ] Button click registers within 2s of tap
- [ ] Choice forwards to `forward_target` (Discord) within 5s of click
- [ ] STELLA picks up choice within 30s of button tap (her next turn)
- [ ] TTL cleanup runs successfully after 24h
- [ ] Authorization check rejects unauthorized users
- [ ] No regression in `send_exec_approval` shell command flow
- [ ] All existing tests pass; new tests in `test_request_human_approval.py` all pass
