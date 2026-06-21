---
title: "Multi-Project Support Design"
created: 2026-06-20
updated: 2026-06-20
type: concept
tags: [multi-project, support, plugin, wiki, context]
sources: []
confidence: high
---

# Multi-Project Support Design

Skill/plugin berbasis llm-wiki untuk multi-project support dalam [[egghead-framework]].

## Problem

Framework baseline digunakan untuk semua project Gilang. Setiap project punya:
- Domain berbeda (web app, CLI tool, research, infra)
- Stack berbeda (Python, TypeScript, Go)
- Konteks berbeda (kode, dokumentasi, deployment)
- Timeline berbeda

Agent harus bisa switch context antar project tanpa cross-contamination.

## Architecture

```
~/.hermes/
├── knowledge/
│   ├── global/
│   │   └── USER_PROFILE.md          # Shared user profile
│   ├── topics/
│   │   └── egghead-framework.md     # Framework knowledge
│   └── repos/
│       ├── project-a/               # Project A context
│       │   ├── CONTEXT.md
│       │   ├── architecture.md
│       │   └── conventions.md
│       ├── project-b/               # Project B context
│       │   ├── CONTEXT.md
│       │   ├── architecture.md
│       │   └── conventions.md
│       └── project-c/
│           └── ...
│
├── wiki/                            # LLM-Wiki (shared)
│   ├── SCHEMA.md
│   ├── index.md
│   ├── concepts/                    # Framework concepts
│   ├── entities/                    # Agent definitions
│   └── projects/                    # Project-specific wiki pages
│       ├── project-a/
│       │   ├── concepts/
│       │   ├── entities/
│       │   └── raw/
│       └── project-b/
│           └── ...
```

## Project Context Switching

### Automatic Detection

When agent starts a session:
1. Check CWD — if in a known project repo, load that project's context
2. Check git remote — match to registered projects
3. Load project-specific CONTEXT.md, architecture, conventions
4. Load project-specific wiki pages (if any)

### Manual Switching

```markdown
# Switch to project: [project-name]

## Actions
1. Load repos/[project-name]/CONTEXT.md
2. Load repos/[project-name]/architecture.md
3. Update todo list with project-specific tasks
4. Set project tag for cost tracking
```

## LLM-Wiki Multi-Project Extension

### Wiki Directory Structure

```
~/egghead-wiki/
├── SCHEMA.md              # Shared schema
├── index.md               # Global index
├── concepts/              # Shared framework concepts
├── entities/              # Shared agent definitions
├── projects/              # Project-specific content
│   ├── project-a/
│   │   ├── SCHEMA.md      # Project-specific overrides
│   │   ├── index.md       # Project index
│   │   ├── concepts/      # Project-specific concepts
│   │   ├── entities/      # Project-specific entities
│   │   └── raw/           # Project-specific sources
│   └── project-b/
│       └── ...
└── shared/                # Cross-project shared knowledge
    ├── patterns/          # Reusable code patterns
    └── decisions/         # Cross-project ADRs
```

### Lint Extension

Wiki lint script extended for multi-project:

```python
# Multi-project lint
for project in get_projects():
    wiki = f"~/egghead-wiki/projects/{project}"
    # Run standard lint
    # Check project-specific tags against SCHEMA
    # Verify cross-project references
    # Check for stale project pages
```

## Project Registration

```yaml
# docs/projects.yaml
projects:
  - id: project-a
    name: "My Web App"
    repo: "~/repos/project-a"
    wiki_path: "~/egghead-wiki/projects/project-a"
    status: active
    created: "2026-06-20"

  - id: project-b
    name: "CLI Tool"
    repo: "~/repos/project-b"
    wiki_path: "~/egghead-wiki/projects/project-b"
    status: active
    created: "2026-06-21"
```

## Cost Tracking Per Project

```yaml
# docs/project-costs.yaml
costs:
  project-a:
    june-2026:
      total_tasks: 25
      total_tokens: 1250000
      total_cost: $6.25
      model_breakdown:
        haiku: 70%
        sonnet: 25%
        opus: 5%

  project-b:
    june-2026:
      total_tasks: 10
      total_tokens: 500000
      total_cost: $2.50
```

## Skill: project-switch

```yaml
---
name: project-switch
description: "Switch active project context. Load project-specific knowledge, wiki, and conventions."
version: 1.0.0
author: Egghead
metadata:
  hermes:
    tags: [project, context, switching]
    category: workflow
---
```

### Steps

1. Read `docs/projects.yaml` for project list
2. Match requested project name
3. Load `repos/[project]/CONTEXT.md`
4. Load `repos/[project]/architecture.md`
5. Load `repos/[project]/conventions.md`
6. Update todo list with project context
7. Confirm switch to user

## See Also

- [[egghead-framework]] — Framework overview
- [[runtime-integration]] — Knowledge architecture
- [[hermes-wiki-research]] — LLM-Wiki implementation
