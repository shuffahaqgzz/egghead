# Wiki Schema

## Domain
Egghead — AI-Driven Multi-Agent Framework. Multi-agent orchestration, agent protocols, workflow engines, inter-agent communication, task delegation, and autonomous agent coordination.

## Conventions
- Filenames: lowercase + hyphens, no spaces (e.g., `agent-orchestrator.md`)
- Each wiki page must start with YAML frontmatter (see below)
- Use `[[wikilinks]]` to link between pages (minimum 2 outbound links per page)
- When updating a page, always bump the `updated` date
- Every new page must be added to `index.md` under the correct section
- Every action must be appended to `log.md`
- **Provenance markers:** On pages that synthesize 3+ sources, append `^[raw/articles/source-file.md]`
  at the end of paragraphs whose claims come from a specific source. This lets a reader trace each
  claim back without re-reading the whole raw file. Optional on single-source pages where the
  `sources:` frontmatter is enough.

## Frontmatter
```yaml
---
title: Page Title
created: YYYY-MM-DD
updated: YYYY-MM-DD
type: entity | concept | comparison | query | summary
tags: [from taxonomy below]
sources: [raw/articles/source-name.md]
# Optional quality signals:
confidence: high | medium | low        # how well-supported the claims are
contested: true                        # set when the page has unresolved contradictions
contradictions: [other-page-slug]      # pages this one conflicts with
---
```

### Frontmatter Field Rules

- **`confidence: high`** — Claim supported by 2+ independent sources or official docs.
- **`confidence: medium`** — Single source, or claim is inference/opinion rather than fact.
- **`confidence: low`** — Speculative, fast-moving topic, or unverified claim.
- **`contested: true`** — Page has unresolved contradictions. Set this AND list
  conflicting pages in `contradictions:[]`. Lint surfaces these for review.
- **`contradictions: [page-slug]`** — Bidirectional: if page A lists page B, page B
  should list page A.
- Default when omitted: treat as `confidence: medium` (conservative assumption).

### Raw Source Frontmatter

Files in `raw/` MUST have a small frontmatter block so re-ingests can detect drift:

```yaml
---
source_url: https://example.com/article   # original URL, if applicable
ingested: YYYY-MM-DD
sha256: <hex digest of the raw content below the frontmatter>
---
```

- Compute `sha256` over the body only (everything after the closing `---`), not the frontmatter itself.
- On re-ingest of the same URL: recompute the sha256, compare to the stored value.
  - Identical → skip processing (source unchanged).
  - Different → flag drift, update raw file, reprocess wiki pages that cite this source.

## Tag Taxonomy
- **Architecture**: architecture, module, component, interface, orchestration, workflow, pipeline, state-machine, event-driven, pub-sub
- **Protocols**: protocol, a2a, mcp, acp, grpc, rest, websocket, message-queue
- **Agents**: agent, multi-agent, subagent, coordinator, supervisor, worker, planner, executor, reviewer
- **Communication**: communication, inter-agent, handoff, delegation, broadcast, multicast
- **Memory**: memory, shared-state, blackboard, context, session, persistence
- **Planning**: planning, task-decomposition, goal, dependency, scheduling, priority
- **Tools**: tool, tool-use, function-calling, capability, registry, discovery
- **Evaluation**: evaluation, benchmark, metrics, testing, validation, scoring
- **Deployment**: deployment, scaling, docker, kubernetes, cloud, edge
- **Security**: security, sandbox, permission, isolation, trust, auth
- **Meta**: comparison, architecture-diagram, code-pattern, best-practice, changelog, update, implementation, design-pattern

## Page Thresholds
- **Create a page** when an entity/concept appears in 2+ sources OR is central to one source
- **Add to existing page** when a source mentions something already covered
- **DON'T create a page** for passing mentions, minor details, or things outside the domain
- **Split a page** when it exceeds ~200 lines — break into sub-topics with cross-links
- **Archive a page** when its content is fully superseded — move to `_archive/`, remove from index

### Split Strategy
When splitting an oversized page:
1. Identify logical sub-sections (e.g., "Layer 1", "Layer 2", or "API", "Internals")
2. Create a new page per sub-section with its own frontmatter
3. The original page becomes an overview with `[[wikilinks]]` to sub-pages
4. Update `index.md` to list both original and new pages
5. Update any external pages that linked to the original — point to the specific sub-page

## Entity Pages
One page per entity. Includes:
- Overview / What it is
- Key facts (file paths, class names, key functions)
- Relationships with other entities (use wiki bidirectional links)
- Source code references

## Concept Pages
One page per concept. Includes:
- Definition / Explanation
- Current knowledge state
- Open questions or controversies
- Related concepts (use wiki bidirectional links)

## Comparison Pages
Comparative analysis. Includes:
- What is being compared and why
- Comparison dimensions (table format recommended)
- Conclusions or synthesis
- Sources

## Update Policy
When new information conflicts with existing content:
1. Check dates — newer sources generally supersede older ones
2. If genuinely contradictory, note both positions with dates and sources
3. Mark the contradiction in frontmatter: `contradictions: [page-name]`
4. Set `contested: true` on both conflicting pages
5. Flag for user review in the lint report
6. Do NOT silently overwrite — contradictions must be explicit

### Confidence Update Rules
When adding new information to an existing page:
- If the new source corroborates existing claims → confidence can be bumped up
- If the new source contradicts → set `contested: true`, don't bump confidence
- If the new source is the only support for a claim → `confidence: medium` at most
- Review `confidence: low` pages during lint — either find corroboration or archive
