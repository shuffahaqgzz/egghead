# KUMACHII-STELLA Framework Comparison and Merge Notes

**Version:** 1.1.0  
**Output Naming Format:** lowercase kebab-case with semantic version  
**Compared Files:**

- `KUMACHII-STELLA-Multi-Agent-Workflow-Framework.md`
- `KUMACHII-STELLA-Multi-Agent-Workflow-Framework-updated.md`

---

## 1. Final Decision

Use the updated file as the base structure, then merge back the strongest missing parts from the original file.

Final output:

```text
kumachii-stella-multi-agent-workflow-framework-v1.1.0-final.md
```

Reason:

- The updated file has stronger RAG, operations, model strategy, tooling, and quality gate coverage.
- The original file has stronger KUMACHII-first intake, runtime deployment model, command references, testing checklist, rollback, memory model, and end-to-end delegation test.

---

## 2. Main Differences

| Area | Original File | Updated File | Final Merge Decision |
|---|---|---|---|
| Title | `Workflow Framework` | `Workflow & Framework` | Use cleaner `Workflow Framework` |
| Objective | Good separation of KUMACHII/STELLA | More complete scope | Merge both |
| Architecture | KUMACHII-first layered architecture | STELLA-first architecture | Use KUMACHII as intake and STELLA as orchestrator |
| Agent list | Complete 10-agent catalog | Complete 10-agent roster with access level | Use updated table with original runtime clarity |
| Deployment modes | Less explicit | Strong light/dedicated/hybrid mode | Use updated |
| Agent responsibilities | Complete but more repetitive | Better contract style with inputs/outputs | Use updated, add original constraints |
| Workflow lifecycle | Phases 0–5 | Phases 0–6 with operate/improve | Use updated Phase 6 |
| Delegation | Good matrix | Better approval column | Use updated, add original personal/office rows |
| Handoff | YAML packet | Markdown protocol | Include both |
| Runtime model | Strong Hermes profile details | Shorter profile mapping | Use original details with updated structure |
| Gateway rules | Strong token/env/provider rules | Strong concise rules | Merge both |
| Model strategy | Missing | Present | Use updated |
| RAG architecture | Missing or light | Strong BONNEY RAG architecture | Use updated |
| Security | Strong isolation | Strong release blocking | Merge both |
| Memory model | Present | Mostly missing | Restore original memory model |
| Commissioning/testing | Strong detailed tests | Good checklist | Merge both |
| Troubleshooting/rollback | Strong original | Good updated | Merge both |
| AGENTS.md | Good | More complete with RAG rules | Use updated and add KUMACHII/STELLA split |
| Final notes | Good | Good next iteration | Merge both |

---

## 3. Key Improvements in Final Version

### 3.1 KUMACHII and STELLA Separation

Final rule:

```text
KUMACHII = personal assistant and user-facing intake
STELLA   = orchestrator, workflow state owner, and project controller
```

This avoids accidental merge of personal assistant and project orchestrator responsibilities.

### 3.2 Stronger Architecture

The final architecture uses layered flow:

```text
User → KUMACHII → STELLA → Specialist Agents → Knowledge/Tool/Runtime Layer
```

This preserves personal assistant usability while keeping project delivery controlled.

### 3.3 Phase 6 Added

The final framework keeps the updated file’s operations phase:

```text
Phase 6 — Operate, Monitor, and Improve
```

This makes the framework production-oriented, not only delivery-oriented.

### 3.4 BONNEY Expanded into Documentation and RAG

BONNEY is kept as one agent, not split.

Reason:

- documentation and RAG need the same source-of-truth discipline
- RAG ingestion should follow documentation authority hierarchy
- one owner prevents stale or duplicated knowledge

### 3.5 Runtime and Testing Details Restored

The final version restores original details for:

- Hermes profile structure
- systemd service names
- credential isolation test
- token conflict test
- end-to-end delegation test
- rollback to KUMACHII only

---

## 4. Naming Format Change

Old format:

```text
KUMACHII-STELLA-Multi-Agent-Workflow-Framework.md
KUMACHII-STELLA-Multi-Agent-Workflow-Framework-updated.md
```

New format:

```text
kumachii-stella-multi-agent-workflow-framework-v1.1.0-final.md
kumachii-stella-framework-comparison-merge-notes-v1.1.0.md
```

Rules:

- lowercase
- kebab-case
- semantic version included
- final status included only in final deliverable filename
- comparison file separated from final framework

---

## 5. Merge Policy Used

When content conflicted:

1. Prefer clearer role separation.
2. Prefer operationally safer rule.
3. Prefer phase-gated process.
4. Prefer explicit approval for risky action.
5. Prefer source-of-truth documents over generated summaries.
6. Prefer dedicated profile for high-risk agents.
7. Prefer compact wording over repeated paragraphs.

---

## 6. Recommended Use

Use the final Markdown file as:

- master architecture document
- deployment reference
- agent operating framework
- source for `AGENTS.md`
- source for creating dedicated agent prompts
- source for RAG ingestion after secrets are removed

Do not use the comparison notes as runtime instructions.

