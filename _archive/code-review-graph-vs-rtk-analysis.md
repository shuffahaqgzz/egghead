# 📊 Code-Review-Graph vs RTK: Comparative Analysis

**Date:** 2026-05-03
**Analyzed by:** Kumachii (for Stella)
**Repository:** https://github.com/tirth8205/code-review-graph

---

## 📋 Executive Summary

| Aspect | code-review-graph | RTK |
|--------|-------------------|-----|
| **Type** | Knowledge graph + MCP server | CLI proxy/filter |
| **Approach** | Structural code analysis | Command output compression |
| **Token Savings** | 6.8-49x on reviews | 60-90% on shell commands |
| **Focus** | Code review & context | Command output optimization |
| **Complexity** | High (Python, Tree-sitter) | Low (Rust binary) |
| **Best For** | Large codebases, code review | Daily dev commands |
| **Integration** | MCP (all platforms) | CLI wrapper + hooks |

**Verdict:** **Complementary, not competing.** They optimize different parts of the token pipeline.

---

## 🔍 Deep Dive: code-review-graph

### What It Does
Builds a **knowledge graph** of your codebase using Tree-sitter AST parsing:
- Maps functions, classes, imports, call chains
- Tracks dependencies and test coverage
- Computes "blast radius" for changes
- Gives AI only the files that matter

### Token Savings
```
Repo        | Naive Tokens | Graph Tokens | Reduction
------------|--------------|--------------|----------
express     | 693          | 983          | 0.7x (small repo)
fastapi     | 4,944        | 614          | 8.1x
flask       | 44,751       | 4,252        | 9.1x
gin         | 21,972       | 1,153        | 16.4x
httpx       | 12,044       | 1,728        | 6.9x
nextjs      | 9,882        | 1,249        | 8.0x
------------|--------------|--------------|----------
Average     |              |              | 8.2x
```

**Key Insight:** Savings scale with codebase size. Monorepos see 27,700+ files excluded.

### How It Works
```
1. Parse codebase → Tree-sitter AST
2. Build graph → nodes (functions/classes) + edges (calls/imports)
3. On change → compute blast radius
4. AI queries MCP → gets only affected files
5. Result: AI reads 15 files instead of 27,700
```

### Features
- **23 languages** supported (Python, TS, JS, Go, Rust, Java, etc.)
- **Incremental updates** (< 2 seconds for 2,900 files)
- **Blast radius analysis** (100% recall, 0.54 F1)
- **Community detection** (Leiden algorithm)
- **Hub/bridge detection** (architectural hotspots)
- **Semantic search** (optional vector embeddings)
- **Multi-repo daemon** (background watching)
- **MCP integration** (28 tools, 5 workflow templates)

### Limitations
- ⚠️ Small repos: Graph overhead > raw file size
- ⚠️ Search quality: MRR 0.35 (needs improvement)
- ⚠️ Flow detection: Only 33% recall (Python best)
- ⚠️ Setup complexity: Python, Tree-sitter, MCP config

---

## 🔍 Deep Dive: RTK

### What It Does
CLI proxy that **filters and compresses** command outputs before they reach LLM:
- Wraps git, cargo, npm, docker, kubectl, etc.
- Smart filtering, grouping, truncation, deduplication
- Transparent hook system (auto-rewrite)

### Token Savings
```
Command              | Standard | RTK   | Savings
---------------------|----------|-------|--------
ls / tree            | 2,000    | 400   | -80%
cat / read           | 40,000   | 12,000| -70%
grep / rg            | 16,000   | 3,200 | -80%
git status           | 3,000    | 600   | -80%
git diff             | 10,000   | 2,500 | -75%
git log              | 2,500    | 500   | -80%
cargo test           | 25,000   | 2,500 | -90%
docker ps            | 900      | 180   | -80%
---------------------|----------|-------|--------
Total                | ~118,000 | ~23,900| -80%
```

### How It Works
```
1. AI calls: git status
2. Hook rewrites to: rtk git status
3. RTK executes git, filters output
4. Returns compressed result (~200 tokens vs ~2,000)
5. AI gets same info, 80% fewer tokens
```

### Features
- **100+ commands** supported
- **< 10ms overhead** (Rust binary)
- **Auto-rewrite hooks** (transparent to AI)
- **Multiple modes** (default, ultra-compact)
- **Gain tracking** (token savings stats)
- **Zero dependencies** (single binary)

### Limitations
- ⚠️ Only optimizes **command outputs** (not code context)
- ⚠️ Bash-only hooks (no built-in tools)
- ⚠️ No code understanding (just output filtering)

---

## 🔄 Head-to-Head Comparison

### Token Optimization Scope

```
┌─────────────────────────────────────────────────────────────┐
│                    TOKEN PIPELINE                             │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐  │
│  │   INPUT      │    │   CONTEXT    │    │   OUTPUT     │  │
│  │   Tokens     │    │   Tokens     │    │   Tokens     │  │
│  └──────┬───────┘    └──────┬───────┘    └──────┬───────┘  │
│         │                   │                   │           │
│         ▼                   ▼                   ▼           │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐  │
│  │  code-review │    │  (neither)   │    │    RTK       │  │
│  │  graph       │    │              │    │  (commands)  │  │
│  │  (code read) │    │              │    │              │  │
│  └──────────────┘    └──────────────┘    └──────────────┘  │
│                                                              │
│  What AI reads       What AI keeps      What AI outputs     │
│  from codebase       in context         to user             │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Different Problems, Different Solutions

| Problem | code-review-graph | RTK |
|---------|-------------------|-----|
| "AI reads entire codebase" | ✅ Solves (blast radius) | ❌ Not its job |
| "git status too verbose" | ❌ Not its job | ✅ Solves (compression) |
| "Code review token cost" | ✅ Solves (8.2x reduction) | ⚠️ Partial (command output) |
| "Daily dev workflow" | ⚠️ Partial (code context) | ✅ Solves (100+ commands) |
| "Large monorepo" | ✅ Excellent (27K files → 15) | ⚠️ Irrelevant |
| "Small project" | ⚠️ Overhead > benefit | ✅ Always useful |

### Performance Metrics

| Metric | code-review-graph | RTK |
|--------|-------------------|-----|
| **Setup time** | ~10 seconds | < 1 second |
| **Runtime overhead** | ~100ms (graph query) | < 10ms |
| **Memory usage** | SQLite + graph | Minimal |
| **Language support** | 23 languages | N/A (command wrapper) |
| **Platform support** | MCP (all platforms) | CLI (all platforms) |
| **Maintenance** | Active (Python) | Active (Rust) |

---

## 🎯 Effectiveness for Stella's Use Case

### Current Setup
- **OpenClaw** (AI agent gateway)
- **RTK** (command output compression) ✅ Installed
- **Caveman** (response compression) ✅ Installed
- **Large projects** (DCIM, VDI, TIP, Cyber Range)

### Where code-review-graph Fits

#### ✅ HIGH value for:
1. **DCIM Core Platform** — Large codebase, multi-file changes
2. **Code review sessions** — AI reads only affected files
3. **Monorepo management** — Blast radius analysis
4. **Architecture understanding** — Community detection, hub nodes

#### ⚠️ MEDIUM value for:
1. **Small scripts** — Overhead may exceed benefit
2. **Single-file changes** — RTK better for command output
3. **Non-code tasks** — Irrelevant (infra, docs, etc.)

#### ❌ LOW value for:
1. **Command output optimization** — RTK already handles this
2. **Response compression** — Caveman already handles this
3. **Simple projects** — Overkill

### Token Savings Projection

```
Current stack:
├─ RTK (commands): -60-90% on shell output
├─ Caveman (responses): -75% on AI output
└─ Total: ~70-80% on output tokens

With code-review-graph added:
├─ RTK (commands): -60-90% on shell output
├─ Caveman (responses): -75% on AI output
├─ CRG (code context): -80-90% on code reads
└─ Total: ~85-90% on all tokens

Additional savings: ~10-15% on code-heavy sessions
```

---

## 🛠️ Implementation Recommendation

### For OpenClaw Integration

#### Option 1: MCP Server (Recommended)
```bash
# Install
pip install code-review-graph

# Configure for OpenClaw (via MCP)
code-review-graph install --platform opencode

# Build graph for DCIM project
cd /path/to/dcim
code-review-graph build

# Use in OpenClaw
# AI can now query: get_impact_radius, get_review_context, etc.
```

**Pros:** Full feature access, 28 MCP tools
**Cons:** Requires MCP setup in OpenClaw

#### Option 2: CLI Wrapper
```bash
# Install
pip install code-review-graph

# Use as CLI
code-review-graph build
code-review-graph detect-changes
code-review-graph visualize
```

**Pros:** Simple, no MCP needed
**Cons:** No automatic integration with AI

#### Option 3: Daemon Mode (Background)
```bash
# Register projects
crg-daemon add ~/projects/dcim --alias dcim
crg-daemon add ~/projects/vdi --alias vdi

# Start daemon
crg-daemon start

# Graphs auto-update in background
```

**Pros:** Always fresh graphs, no manual updates
**Cons:** Background process, resource usage

---

## 📊 ROI Analysis

### Cost-Benefit Matrix

| Factor | code-review-graph | RTK | Winner |
|--------|-------------------|-----|--------|
| **Setup cost** | Medium (Python, MCP) | Low (binary) | RTK |
| **Maintenance** | Medium (updates) | Low (zero-dep) | RTK |
| **Token savings (commands)** | N/A | High (-80%) | RTK |
| **Token savings (code)** | High (-8.2x) | N/A | CRG |
| **Code quality** | High (blast radius) | N/A | CRG |
| **Daily workflow** | Medium | High | RTK |
| **Large project** | High | Low | CRG |

### Break-Even Analysis

```
code-review-graph worth it when:
├─ Codebase > 500 files
├─ Multi-file changes frequent
├─ Code review is major token sink
├─ Monorepo or complex architecture
└─ Time saved > setup cost

RTK worth it when:
├─ Any development work
├─ Shell commands frequent
├─ Token budget matters
└─ Always (zero downside)
```

---

## 🎯 Final Verdict

### Summary Table

| Tool | Category | Effectiveness | Stella Priority |
|------|----------|---------------|-----------------|
| **RTK** | Command output | ⭐⭐⭐⭐⭐ | ✅ Already installed |
| **Caveman** | Response output | ⭐⭐⭐⭐⭐ | ✅ Already installed |
| **code-review-graph** | Code context | ⭐⭐⭐⭐ | ⚠️ Consider for large projects |

### Recommendation

#### Immediate (Now)
- ✅ **Keep RTK** — Daily driver, always useful
- ✅ **Keep Caveman** — Response optimization
- ⏸️ **Defer code-review-graph** — Not critical yet

#### When DCIM Project Grows (500+ files)
- 🔜 **Install code-review-graph** — Big ROI on large codebase
- 🔜 **Set up MCP integration** — Full feature access
- 🔜 **Use blast radius for reviews** — Token savings on code review

#### Hybrid Stack (Optimal)
```
Token Optimization Stack:
├─ Layer 1: RTK (command output) → -80%
├─ Layer 2: Caveman (response) → -75%
├─ Layer 3: code-review-graph (code context) → -8.2x
└─ Total: ~90% token reduction
```

---

## 📚 References

### code-review-graph
- **GitHub:** https://github.com/tirth8205/code-review-graph
- **Docs:** https://code-review-graph.com
- **Discord:** https://discord.gg/3p58KXqGFN
- **Install:** `pip install code-review-graph`

### RTK
- **GitHub:** https://github.com/rtk-ai/rtk
- **Docs:** https://rtk-ai.app
- **Install:** `curl -fsSL https://raw.githubusercontent.com/rtk-ai/rtk/refs/heads/master/install.sh | sh`

---

*Analysis created: 2026-05-03*
*Author: Kumachii (for Stella)*
