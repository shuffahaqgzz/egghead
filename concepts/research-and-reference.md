---
title: Research and Reference
created: 2026-06-20
updated: 2026-06-20
type: concept
tags:
  - research
  - reference
  - hermes-internals
  - tools
  - providers
  - opencode
sources:
  - research-opencode-hermes-multiagent.md
  - HERMES_INTERNALS.md
  - Kumachii_VS_HERMES_TOOLS.md
  - ENOWX_PROVIDER.md
  - hermes-wiki-research-2026-05-18.md
---

# Research and Reference

Research findings, Hermes internals documentation, tool mapping, and provider configuration for the multi-agent system.

## opencode-hermes-multiagent Research

Multi-agent orchestration system for OpenCode AI. 17 specialized subagents in 6 domains, orchestrated by a master agent ("Hermes") that never touches code directly.

### Architecture: Hub-and-Spoke Model
```text
                    ┌──────────────┐
                    │   HERMES     │
                    │  (Master)    │
                    │ 3 tools only │
                    └──────┬───────┘
                           │ dispatch
        ┌──────────────────┼──────────────────┐
        │                  │                  │
   ┌────▼────┐      ┌─────▼─────┐     ┌─────▼─────┐
   │Research │      │Planning   │     │Implement  │
   │ finder  │      │ architect │     │ coder     │
   │ analyst │      │ planner   │     │ editor    │
   └─────────┘      └───────────┘     │ fixer     │
                                      │ refactorer│
   ┌─────────┐      ┌───────────┐     └───────────┘
   │Quality  │      │Infra      │     ┌───────────┐
   │ reviewer│      │ devops    │     │Docs       │
   │ tester  │      │ optimizer │     │ documenter│
   │ debugger│      └───────────┘     │ commenter │
   │ security│                        └───────────┘
   └─────────┘
```

### Pipeline System (7 Pipelines)
| Pipeline | Sequence |
|---|---|
| New Feature | finder → analyst → architect → planner → coder → reviewer → tester → documenter |
| Security Feature | finder → analyst → researcher → architect → planner → coder → reviewer → security → tester → documenter |
| Bug (unknown) | finder → debugger → fixer → reviewer → tester |
| Bug (known) | finder → fixer → reviewer → tester |
| Refactoring | finder → analyst → refactorer → reviewer → tester |
| Performance | finder → analyst → optimizer → reviewer → tester |
| Infrastructure | finder → devops → reviewer → tester |

### Comparison with Framework v1.1.0
| Hermes Agent | v1.1.0 Agent | Overlap |
|---|---|---|
| Architect | SHAKA | HIGH |
| Coder | EDISON | HIGH |
| Researcher | PYTHAGORAS | HIGH (Hermes splits into 3) |
| Security | ATLAS | HIGH |
| Tester | YORK | HIGH |
| DevOps | LILITH | HIGH |
| Documenter | BONNEY | HIGH |
| — | KUMACHII (intake) | MISSING in Hermes |
| — | STUSSY (monitoring) | MISSING in Hermes |
| Finder, Analyst | — | MISSING in v1.1.0 |
| Debugger | — | MISSING in v1.1.0 |

## Hermes Internals

### Runtime Overview
- **Machine:** Ubuntu 24.04 LTS (10.50.0.111)
- **Agent Runtime:** Hermes Agent (Nous Research)
- **Model Router:** 9Router auto-thinking (default Haiku 4.5, route to Opus 4.7 for heavy tasks)
- **Session Transport:** Subagent (fork-based, isolated context)
- **Tool Transport:** Native tool calls + MCP (Model Context Protocol) autodiscovery

### Directory Structure
```text
~/.hermes/
├── .env                    # Credentials, API keys
├── SOUL.md                 # Personality, mandate, workflow
├── skills/                 # Skill library
├── knowledge/              # Layered knowledge base
│   ├── global/
│   │   └── USER_PROFILE.md
│   ├── topics/
│   └── repos/
├── data/                   # Persistent runtime data
├── handoffs/               # Session handoff documents
├── cron/                   # Cron job output
└── plans/                  # Implementation plans
```

### Tool Discovery
```bash
hermes tools  # List all available tools
```

Built-in tools: file operations, terminal, web, vision, delegation, memory, messaging, scheduling, skills, todo.

### Model Router (9Router)
- **Default:** Claude Haiku 4.5 (fast, low latency)
- **Heavy tasks:** Routes to Opus 4.7
- **Performance targets:** Q&A <2s, code review <5s, complex reasoning <10s

## Kumachii vs Hermes Tools

### Tool Mapping
| Category | Kumachii Tool | Hermes Equivalent |
|---|---|---|
| File System | `file.read`, `file.write` | `read_file`, `write_file` |
| Terminal | `bash.run` | `terminal` |
| Web | `web.search` | `web_search` |
| Vision | `vision.analyze` | `vision_analyze` |
| Delegation | `delegate` | `delegate_task` |
| Memory | `memory.recall`, `memory.save` | `memory` |
| Scheduling | `cron.create` | `cronjob` |

### Critical Differences
1. **Code Execution:** Kumachii unlimited vs Hermes constrained (5min timeout)
2. **Git & PR:** Kumachii native vs Hermes CLI + skills
3. **Delegation:** Kumachii synchronous vs Hermes isolated subagent pool (max 3)
4. **Browser:** Kumachii Playwright vs Hermes vision-based

## EnowX Provider

### Configuration
```yaml
providers:
  enowxlabs:
    api_key: ${ENOWXLABS_API_KEY}
    base_url: "https://api.enowxlabs.com/v1"
    models:
      - enowx-custom-v1
      - enowx-domain-specialist-v2
```

### When to Use
- Custom model outperforms Anthropic Claude
- Cost savings > operational complexity
- Specialized domain performance matters

### When to Skip
- API key not setup
- Service unavailable
- Latency critical
- Task doesn't warrant specialized model

## Hermes-Wiki Research

### Architecture Reference
Hermes-Wiki (`/home/infra/hermes-wiki/`) provides verified architecture documentation for the Hermes Agent system.

### Key Patterns to Adopt
1. **Multi-Agent Architecture:** MAX_DEPTH=2, MAX_CONCURRENT_CHILDREN=3, DEFAULT_MAX_ITERATIONS=50
2. **Skills System:** Progressive disclosure, curator, pinned skills
3. **Skills + Memory Interaction:** Memory = facts, Skills = procedural workflows
4. **Configuration & Multi-Profile:** Each profile = full HERMES_HOME directory

### Wondelai Skills (42 agent-skills)
High-fit skills for this framework: 10 of 42 directly applicable to EDISON, SHAKA, BONNEY, KUMACHII profiles.

## Related Pages

- [[hymes-multi-agent-architecture]] - Architecture implementation
- [[multi-agent-workflow-framework]] - Framework specification
- [[token-optimization]] - RTK and code-review-graph analysis
