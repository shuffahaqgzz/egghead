---
title: "Hermes Internals"
created: 2026-06-20
updated: 2026-06-20
type: concept
tags: [hermes, runtime, config, architecture]
sources: [_archive/framework-level/HERMES_INTERNALS.md]
confidence: high
---

# Hermes Internals

Runtime config, setup, dan architecture reference untuk Hermes Agent.

## Runtime Overview

- **Agent Runtime:** Hermes Agent (Nous Research)
- **Session Transport:** Subagent (fork-based, isolated context)
- **Tool Transport:** Native tool calls + MCP (Model Context Protocol)

## Directory Structure

```
~/.hermes/
├── .env                    # Credentials, API keys
├── SOUL.md                 # Personality, mandate, workflow
├── skills/                 # Skill library
├── knowledge/              # Layered knowledge base
│   ├── global/             # User preferences
│   ├── topics/             # Domain knowledge
│   └── repos/<name>/       # Per-project knowledge
├── data/                   # Persistent runtime data
├── handoffs/               # Session handoff documents
├── cron/                   # Cron job output
└── plans/                  # Implementation plans
```

## Tool Discovery

Built-in tools always available:
- File operations (read_file, write_file, search_files, patch)
- Terminal (terminal, process)
- Web (web_search, web_extract, browser_*)
- Delegation (delegate_task)
- Memory (memory, session_search, hindsight_*)
- Skills (skill_view, skills_list, skill_manage)
- Scheduling (cronjob)

MCP servers auto-discovered via config.yaml.

## Knowledge Injection

At turn start, system injects knowledge:
1. Global knowledge (always)
2. Topic knowledge (if keyword match)
3. Repo knowledge (if CWD match)

Sampling: <5KB full, 5-20KB summarized, >20KB excerpted. ~10KB cap per turn.

## See Also

- [[egghead-framework]] — Framework overview
- [[runtime-integration]] — How Egghead maps to Hermes
