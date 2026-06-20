# Wiki Index

> Content catalog. Every wiki page listed under its type with a one-line summary.
> Read this first to find relevant pages for any query.
> Last updated: 2026-06-19 | Total pages: 30

## Entities

- [[KUMACHII]] — Personal assistant + intake agent (Telegram primary)
- [[STELLA]] — Orchestrator + workflow owner (Discord inter-agent, Telegram approvals)
- [[SHAKA]] — Architect agent (ADRs, system design)
- [[EDISON]] — Coding agent (TDD executor)
- [[PYTHAGORAS]] — Research agent (read-only)
- [[ATLAS]] — Security review agent (can block)
- [[YORK]] — QA / reviewer agent (can block)
- [[LILITH]] — DevOps agent (gated deploy)
- [[BONNEY]] — Documentation + RAG agent
- [[STUSSY]] — Monitoring + operations agent (can block)

## Concepts

### Architecture & Framework
- [[overall-architecture]] — Phase-gated multi-agent workflow with 10 specialized roles
- [[agent-roster]] — 10 agents with roles, access levels, and delegation matrix
- [[authority-hierarchy]] — Source precedence from docs down to chat history
- [[multi-agent-workflow-framework]] — Framework v1.1.0 specification (KUMACHII-STELLA)

### Workflow & Lifecycle
- [[gate-system]] — Quality gates (0–6) enforcing structured phase progression
- [[handoff-protocol]] — Structured markdown template for agent-to-agent transfers
- [[human-approval-tool-and-gate-system]] — Approval tool and gate decision system
- [[hymes-multi-agent-architecture]] — Hermes multi-agent implementation stages

### Security & Deployment
- [[security-model]] — Container hardening, LAN trust, and ATLAS review
- [[deployment-pattern]] — Docker Compose services and Hermes agent profiles
- [[deployment-and-operations]] — Deployment status, E2E testing, live orchestration

### Code & Quality
- [[coding-rules]] — TDD, review checklist, and architecture compliance
- [[rag-manifest]] — Document ingestion tracking with authority levels

### Skills & Memory
- [[skills-architecture]] — Skills propagation and mandatory loading
- [[memory-and-rag-architecture]] — Honcho memory layer and RAG pipeline

### Migration & Planning
- [[phase-5-migration-plan]] — Migration from standalone to v1.1.0
- [[agent-operating-guide]] — Master operating guide for all agents

### Research & Analysis
- [[research-and-reference]] — Research findings, tool mapping, Hermes internals
- [[ai-development-workflow-analysis]] — Framework comparison and token analysis
- [[token-optimization]] — RTK, Caveman, CRG optimization strategies

## Comparisons

## Queries
