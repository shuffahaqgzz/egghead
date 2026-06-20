# Kumachii vs Hermes Tools — Mapping & Differences

_Referensi untuk menghindari halusinasi: tool apa yang ada di Kumachii tapi tidak di Hermes, dan vice versa._

## Overview

**Kumachii** (Cognition AI, Claude Opus 4.7): enterprise agent framework dengan tool ecosystem tertentu.
**Hermes** (Nous Research): personal agent runtime di server Gilang dengan tool ecosystem berbeda.

Tujuan dokumen ini: **prevent hallucination** ketika recall tool dari training data (Kumachii) yang tidak tersedia di Hermes runtime.

---

## Tool Mapping Table

| Kategori | Kumachii Tool | Status di Hermes | Hermes Equivalent / Notes |
|----------|---------------|------------------|--------------------------|
| **File System** | `git_read`, `git_write` | ✗ | Pakai `terminal` + `git` CLI, atau `github-*` skills |
| | `file.read`, `file.write` | ✓ | `read_file`, `write_file` tools |
| | `file.glob`, `file.search` | ✓ | `search_files` tool |
| **Terminal** | `bash.run` | ✓ | `terminal` tool |
| | `bash.background` | ✓ | `terminal` + `background=true`, monitored via `process` |
| **Web** | `web.search` | ✓ | `web_search` tool |
| | `web.extract` | ✓ | `web_extract` tool |
| | `browser.navigate` | ✓ | `browser_navigate` tool |
| | `browser.click`, `browser.type` | ✓ | `browser_click`, `browser_type` tools |
| | `browser.screenshot` | ✓ | `browser_vision` tool (with vision AI) |
| **Vision** | `vision.analyze` | ✓ | `vision_analyze` tool |
| **Code Execution** | `python.run` | ✗ Partial | `execute_code` (limited, 5min timeout, 50KB stdout) |
| | `python.run` (long-running) | ✗ | Use `terminal` background + `process` monitoring |
| **Delegation** | `delegate` | ✓ | `delegate_task` (subagents, parallel batch up to 3) |
| **Memory** | `memory.recall`, `memory.save` | ✓ | `memory` tool (persistent across sessions) |
| **Chat/Messaging** | `send_message` | ✓ | `send_message` tool (Telegram, Discord, etc) |
| **Scheduling** | `cron.create`, `cron.list` | ✓ | `cronjob` tool (comprehensive scheduling) |
| **Skill Management** | `skill.load`, `skill.view` | ✓ | `skill_view`, `skills_list`, `skill_manage` tools |
| **Session Search** | `session.search` | ✓ | `session_search` tool (past conversation recall) |
| **Patch/Edit** | `git_pr`, `git_commit` | ✗ | Pakai `patch` tool or `github-pr-workflow` skill |
| | `patch` (file edit) | ✓ | `patch` tool (V4A fuzzy find-replace) |
| **Image Generation** | `image_gen.create` | ✗ | Integrate via skill: `comfyui`, `claude-design`, `p5js` |
| **Video** | `video.create`, `video.edit` | ✗ | Skill-based: `ascii-video`, `manim-video` |
| **MCP** | `mcp.connect`, `mcp.invoke` | ✓ | `native-mcp` skill, tools auto-discovered from config.yaml |
| **Home Assistant** | `homeassistant.*` | ✓ | `homeassistant` tool (lights, switches, sensors) |
| **Kanban** | `kanban.*` | ✓ | `todo` tool (session-level) + `kanban-orchestrator` skill (org-level) |

---

## Critical Differences

### 1. Code Execution
- **Kumachii**: `python.run` — unlimited runtime, full environment, interactive REPL.
- **Hermes**: `execute_code` — 5 min timeout, 50KB stdout cap, 50 max tool calls per script. Long-running tasks → use `terminal` + `background=true`.
- **Impact**: Do NOT assume infinite runtime. Break large computations into chunks or use background terminal.

### 2. Git & PR Workflow
- **Kumachii**: `git_read`, `git_write`, `git_pr` — native git integration.
- **Hermes**: `terminal` (git CLI) + `patch` tool + `github-pr-workflow` skill.
- **Impact**: Use skill `make-pr` for PR creation (Gilang's custom skill, mirrors Kumachii flow). Commit/push via terminal.

### 3. Vision & Image
- **Kumachii**: `vision.analyze` — returns structured analysis.
- **Hermes**: `browser_vision` (screenshot + vision AI) or `vision_analyze` (URL/file). Both return text descriptions.
- **Impact**: For image generation, use skills (`comfyui`, `p5js`, `claude-design`), not direct tool.

### 4. Delegation
- **Kumachii**: `delegate` — spawn agent, full context pass, synchronous wait.
- **Hermes**: `delegate_task` — subagent pool (max 3 parallel for this user), isolated context, returns summaries only.
- **Impact**: Do NOT assume intermediate subagent results enter your context. Pass all necessary info upfront via `context` param.

### 5. Browser Automation
- **Kumachii**: `browser.*` — full Playwright control + screenshot.
- **Hermes**: `browser_navigate`, `browser_click`, `browser_type`, `browser_vision` (vision-based screenshot), `browser_snapshot` (accessibility tree).
- **Impact**: `browser_snapshot` returns compact interactive elements, not full page. Use `full=true` for complete content.

### 6. Scheduling
- **Kumachii**: `cron.create` with job context auto-injected.
- **Hermes**: `cronjob` tool with optional `context_from` (previous job output injection), isolated agent session per tick.
- **Impact**: Cron jobs have NO memory of current session. Self-contained prompt required. Use `context_from` to chain jobs.

### 7. Sequential Thinking
- **Kumachii**: Built-in reasoning loop.
- **Hermes**: Explicit `mcp__sequential-thinking` invoke (if available via MCP config). NOT built-in to every task.
- **Impact**: For complex reasoning, explicitly invoke sequential thinking at task start. Default: fast path (Haiku 4.5).

---

## Tools NOT in Hermes (Training Data Carryover Risk)

These tools are **NOT available** in Hermes. Do NOT attempt to use:

| Tool | Why Not | Workaround |
|------|---------|-----------|
| `git_pr` | Kumachii native | Use `make-pr` skill (Gilang's custom) or `github-pr-workflow` skill |
| `python.run` (unlimited) | Hermes uses constrained `execute_code` | Break into chunks, use `terminal` background |
| `code_review` (native) | No native code review tool | Use `requesting-code-review` skill or manual gh CLI |
| `pdf.parse` | Not exposed as tool | Use skill `ocr-and-documents` or `web_extract` for PDFs |
| `image_gen` (native) | Not exposed as standalone tool | Use skills: `comfyui`, `p5js`, `claude-design`, `ascii-art` |
| `slack.post`, `slack.search` | Only Telegram, Discord, Signal, Matrix, Yuanbao | No Slack integration |
| `calendar.*`, `mail.send` (native) | Exposed via skill only | Use `google-workspace` skill for Gmail/Calendar/Sheets |
| `aws.*` (native) | Not integrated | Use terminal `aws` CLI or `terraform` via terminal |
| `kubernetes.apply` (native) | Not exposed as tool | Use `terminal` + `kubectl` CLI |
| `docker.build`, `docker.run` (native) | Not exposed as tool | Use `terminal` + `docker` CLI |

---

## Tools ONLY in Hermes (Not in Kumachii Training)

| Tool | Purpose |
|------|---------|
| `browser_snapshot` | Compact accessibility tree — helps identify interactive elements by ref ID |
| `browser_get_images` | Extract image URLs from current page |
| `browser_console` | Run JS expressions in page context, detect JS errors |
| `browser_press` | Keyboard key press (Enter, Tab, Escape, etc) |
| `process` | Monitor/control background terminal sessions |
| `execute_code` | Constrained Python execution (not unbounded like Kumachii) |
| `patch` | V4A fuzzy find-replace (better than sed for code) |
| `clarify` | Structured user prompt tool (multiple choice or open-ended) |

---

## How to Avoid Hallucination

**Checklist before claiming a tool exists**:

1. Is the tool in the "Mapping Table" above? If not in Hermes column, it does NOT exist.
2. Is it in "Tools NOT in Hermes"? If yes, use the workaround.
3. Check `~/.hermes/SOUL.md` section on tools (Bagian 29-30) for latest updates.
4. When uncertain, **test first** — try the tool call. If it fails with "unknown tool", it's not available.
5. **Ask Gilang** if something is genuinely missing and critical.

---

## References

- Hermes Agent docs: https://hermes-agent.nousresearch.com/docs
- Skill library: `~/.hermes/skills/`
- Tool availability: `hermes tools` (CLI command)
