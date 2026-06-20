# Hermes Internals — Runtime Config, Setup & Architecture

_Reference untuk memahami cara Hermes runtime bekerja, config structure, dan dependencies di server Gilang._

---

## Runtime Overview

**Machine**: Ubuntu 24.04 LTS (10.50.0.111)
**Agent Runtime**: Hermes Agent (Nous Research)
**Model Router**: 9Router auto-thinking (default Haiku 4.5, route ke Opus 4.7 untuk task berat)
**Session Transport**: Subagent (fork-based, isolated context)
**Tool Transport**: Native tool calls + MCP (Model Context Protocol) autodiscovery

---

## Directory Structure

```
~/.hermes/
├── .env                    # Credentials, API keys, env vars
├── SOUL.md                 # Personality, mandat, workflow doc (THIS FILE + Bagian 0-30)
├── skills/                 # Skill library (~/.hermes/skills/<category>/<name>/SKILL.md)
│   ├── personal/           # Custom Gilang skills (make-pr, session-handoff, load-context)
│   ├── autonomous-ai-agents/
│   ├── creative/
│   ├── devops/
│   ├── github/
│   ├── mlops/
│   └── [30+ more categories]
├── knowledge/              # Layered knowledge base
│   ├── global/
│   │   └── USER_PROFILE.md # Gilang's profile, preferences, role
│   ├── topics/             # Contextual knowledge by keyword/domain
│   │   ├── Kumachii_VS_HERMES_TOOLS.md
│   │   ├── HERMES_INTERNALS.md (this file)
│   │   ├── ENOWX_PROVIDER.md (optional)
│   │   └── [project-specific topics]
│   └── repos/<name>/       # Per-project knowledge (stack, conventions, gotchas)
├── data/                   # Persistent runtime data directory
├── handoffs/               # Session handoff documents (auto-generated + manual)
├── cron/                   # Cron job output storage
│   └── output/
└── plans/                  # Implementation plans (if using plan mode)
```

---

## Environment Variables (~/.hermes/.env)

Critical vars for Hermes runtime:

| Var | Purpose | Status |
|-----|---------|--------|
| `HERMES_MAX_ITERATIONS` | Max agent turns before auto-compress | Check `.env` |
| `TELEGRAM_BOT_TOKEN` | Bot auth for Telegram integration | Loaded |
| `GITHUB_TOKEN` | GitHub API auth (gh CLI, PR creation) | Loaded |
| `ENOWXLABS_API_KEY` | enowX Labs provider key (if using) | Optional |
| `REDIS_URL` | Redis connection (session state, caching) | Project-specific |
| `DATABASE_URL` | Database connection string | Project-specific |

**View current env**:
```bash
cat ~/.hermes/.env | grep -E "^[A-Z_]+=" | head -20
```

**Add new var**:
```bash
echo "NEW_VAR=value" >> ~/.hermes/.env
```

---

## Tool Discovery & MCP

### Built-in Tools (Always Available)

```bash
# List all available tools
hermes tools

# Output includes:
# - file operations (read_file, write_file, search_files, patch)
# - terminal (terminal, process)
# - web (web_search, web_extract, browser_*)
# - vision (vision_analyze, browser_vision)
# - delegation (delegate_task)
# - memory (memory, session_search)
# - messaging (send_message)
# - scheduling (cronjob)
# - skills (skill_view, skills_list, skill_manage)
# - todo (todo)
```

### MCP Server Integration

MCP servers auto-discovered via config.yaml:

```yaml
# ~/.hermes/config.yaml (or ~/.config/hermes/config.yaml)
mcp_servers:
  sequential-thinking:
    command: mcp-server-sequential-thinking
    args: []
  # [other MCP servers as needed]
```

**Invoke MCP tool** (example):
```
tool: mcp__sequential-thinking
params: { task: "analyze X", depth: "deep" }
```

**Available MCP servers** (check `hermes mcp list`):
- `sequential-thinking` — multi-pass reasoning (explicit invoke, not auto)
- [other servers configured per setup]

---

## Model Router (9Router Auto-Thinking)

### Default Behavior

- **Default model**: Claude Haiku 4.5 (fast, low latency)
- **Trigger for Opus 4.7**: Task complexity, reasoning depth, or explicit routing
- **Router decision**: Based on task tokens, estimated complexity, Gilang's historical usage

### Explicit Model Override

```python
# In cronjob or delegate_task, set model override:
{
  "model": {
    "provider": "anthropic",
    "model": "claude-opus-4-7"
  }
}
```

### Performance Targets

| Task Type | Expected Model | Target Latency |
|-----------|----------------|-----------------|
| Q&A, simple task | Haiku 4.5 | <2s |
| Code review, planning | Opus 4.7 | <5s |
| Complex reasoning (mcp__sequential-thinking) | Opus 4.7 | <10s |
| Delegation (subagent) | Haiku 4.5 (child) | varies |

---

## Skill Management

### Load Skill

```bash
hermes skills view <skill_name>
hermes skills view personal/make-pr
hermes skills view github/github-pr-workflow
```

### Create/Update Skill

```bash
# Via skill_manage tool in conversation
skill_manage(action='create', name='my-skill', category='personal', content='...')
skill_manage(action='patch', name='my-skill', old_string='...', new_string='...')
```

### Skill Structure

```
~/.hermes/skills/<category>/<name>/
├── SKILL.md                    # Main: frontmatter + markdown
├── references/                 # Docs, links, examples
├── templates/                  # Config templates, scripts
├── scripts/                    # Executable helpers
└── assets/                     # Images, diagrams
```

---

## Knowledge Base Injection

### System Injection Logic

At turn start, system injects `<knowledge-hints>` block containing:

1. **Global knowledge** (always): `~/.hermes/knowledge/global/USER_PROFILE.md`
2. **Topic knowledge** (if keyword match): `~/.hermes/knowledge/topics/<topic>.md`
3. **Repo knowledge** (if CWD or git repo match): `~/.hermes/knowledge/repos/<name>/`

### Sampling Strategy

- File size < 5KB: full inject
- File size 5-20KB: LLM-summarized excerpt
- File size > 20KB: LLM-summarized excerpt (first 5KB + keyword-matched sections)
- Total injected knowledge: ~10KB cap per turn

### When to Update Knowledge

**Trigger save to knowledge**:
- User correction ("remember that", "don't do that again")
- Environment discovery (OS, tool version, project stack)
- Recurring gotcha or convention
- Important decision / architectural insight

**Use `memory` tool** (persistent notes):
```python
memory(action='add', target='memory', content='...')
memory(action='add', target='user', content='...')
```

**Use `knowledge/` files** (contextual facts):
- Gilang's profile → `global/USER_PROFILE.md`
- Project stack / gotchas → `repos/<project>/ARCHITECTURE.md` or `STACK.md`
- Domain-specific knowledge → `topics/<domain>.md`

---

## Persistent State Management

### Session Handoff

For long sessions (>20 turns) or milestone completions:

```python
# Trigger session-handoff skill
skill_view(name='personal/session-handoff')
```

Output: `~/.hermes/handoffs/CONTEXT-<date>-<topic>.md`

**Use case**: Before sever disconnects, agent restart, or to preserve complex state for future reference.

### Todo Tracking

Session-level todo list:

```python
todo()  # read current list
todo(todos=[...], merge=false)  # replace list
todo(todos=[...], merge=true)   # update + add
```

Todos are **not** persistent across sessions (by design — they're session-scoped). Use `session_search` to recall past todo patterns.

---

## Background Process Management

### Start Long-Running Task

```python
terminal(command='...',
         background=true,
         notify_on_complete=true,
         timeout=3600)
# Returns: session_id
```

### Monitor Background Process

```python
process(action='poll', session_id=session_id)
process(action='log', session_id=session_id, limit=50)
process(action='wait', session_id=session_id, timeout=300)
process(action='kill', session_id=session_id)
```

---

## Cron & Scheduling

### Create Scheduled Job

```python
cronjob(action='create',
        name='my-job',
        schedule='0 9 * * *',  # 9 AM daily
        prompt='Do X...',
        skills=['skill-a', 'skill-b'],
        deliver='origin')
```

### Job Execution Context

- **Isolated session**: No memory of current chat. Prompt must be self-contained.
- **Context injection**: Use `context_from=['job-id']` to inject previous job output.
- **Output delivery**: Auto-deliver to `deliver` target (default: 'origin' = current chat).

---

## Network & Connectivity

### Local Services

| Service | Port | Purpose |
|---------|------|---------|
| Hermes Gateway | (configured) | API endpoint for agent requests |
| 9Router | 20128 | Model router / LLM dispatch |
| Telegram Bot | (webhook) | Telegram integration |
| [Project services] | varies | Per-project (Next.js 3000, etc) |

### External Services

- **Anthropic API** (Claude models): HTTPS, token auth
- **GitHub API** (gh CLI, REST): HTTPS, token auth
- **Search providers**: HTTPS, rate-limited
- **MCP servers**: Local (stdio) or HTTP

### VPN / Private Network

If task requires access to private/internal endpoints:

1. Check `~/.hermes/.env` for `VPN_CONFIG` or similar
2. If missing, ask Gilang (see SOUL.md Bagian 7.6)
3. Setup VPN client (WireGuard, OpenVPN, etc) via terminal
4. Verify connectivity to target endpoint

---

## Debugging & Troubleshooting

### Check Hermes Health

```bash
hermes version
hermes config list
hermes tools
hermes skills list
```

### Inspect Current Session

```bash
echo $HERMES_SESSION_ID
echo $HERMES_USER
```

### View Recent Logs

```bash
# Hermes runtime logs
tail -100f ~/.hermes/logs/hermes.log 2>/dev/null || echo "Logs not available"

# MCP server logs
hermes mcp logs
```

### Test Tool Availability

```bash
# Try reading a file
python3 -c "from hermes_tools import read_file; print(read_file('~/.hermes/SOUL.md', limit=10))"

# Try terminal
echo "hermes tools status check" && hostname
```

### Report Environment Issues

If tool fails with system-level error (hang, timeout, permission, etc):

1. **Capture verbatim error**
2. **Note reproducibility**: 1st attempt? 3rd? Random?
3. **Report to Gilang** with:
   - Tool + parameters
   - Error message (full)
   - System state (free memory, disk, CPU)
   - Suggested workaround (if any)

---

## Performance & Resource Management

### Typical Resource Usage

- **Idle**: <10MB RAM, <1% CPU
- **Active reasoning**: 200-500MB RAM, 20-30% CPU (single core)
- **Delegation (3 subagents)**: 500-1000MB RAM, 60-80% CPU (4 cores)
- **Long terminal task**: Depends on process

### Optimization Tips

- Use `execute_code` for Python work (native, fast)
- Parallel tool calls over sequential (latency trade-off vs resource)
- Break large tasks into smaller subtasks if memory tight
- Use cron for scheduled work (off-peak execution)

---

## References

- **Config**: `~/.hermes/config.yaml`
- **SOUL.md**: `~/.hermes/SOUL.md` (this agent's personality)
- **Official docs**: https://hermes-agent.nousresearch.com/docs
- **MCP spec**: https://modelcontextprotocol.io/
- **Skill discovery**: `hermes skills list`
