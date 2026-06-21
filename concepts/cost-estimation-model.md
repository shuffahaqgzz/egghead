---
title: "Cost Estimation Model"
created: 2026-06-20
updated: 2026-06-20
type: concept
tags: [cost, estimation, tokens, pricing, optimization]
sources: []
confidence: high
---

# Cost Estimation Model

Token cost estimation untuk [[egghead-framework]].

## Model Pricing (per 1M tokens, June 2026)

### Anthropic Claude

| Model | Input | Output | Cached Input | Best For |
|-------|-------|--------|-------------|----------|
| Haiku 4.5 | $1.00 | $5.00 | $0.10 | Simple tasks |
| Sonnet 4.6 | $3.00 | $15.00 | $0.30 | Quality-sensitive |
| Opus 4.8 | $5.00 | $25.00 | $0.50 | Complex reasoning |

### OpenAI GPT

| Model | Input | Output | Cached Input | Best For |
|-------|-------|--------|-------------|----------|
| GPT-4o-mini | $0.15 | $0.60 | $0.02 | Budget tasks |
| GPT-5.4 mini | $0.75 | $4.50 | $0.075 | Balanced mini |
| GPT-5.4 | $2.50 | $15.00 | $0.25 | Coding |
| GPT-5.5 | $5.00 | $30.00 | $0.50 | Frontier |

### Google Gemini

| Model | Input | Output | Cached Input | Best For |
|-------|-------|--------|-------------|----------|
| Gemini 2.5 Flash | $0.30 | $2.50 | $0.03 | High-volume |
| Gemini 2.5 Pro | $1.00 | $10.00 | $0.125 | Complex reasoning |
| Gemini 3.1 Flash-Lite | $0.25 | $1.50 | — | Cost-efficient |

## Cost Per Phase (Estimated)

Estimasi berdasarkan task "Large" (2-8 hours, full workflow):

| Phase | Agent | Model Tier | Est. Input Tokens | Est. Output Tokens | Est. Cost (Haiku) | Est. Cost (Sonnet) |
|-------|-------|-----------|-------------------|--------------------|--------------------|---------------------|
| 0 Discovery | Intake + Orchestrator | Low | 5K | 3K | $0.02 | $0.06 |
| 1 Planning | Orchestrator | Medium | 15K | 10K | $0.07 | $0.18 |
| 2 Solutioning | Architect | Medium | 20K | 15K | $0.10 | $0.26 |
| 3 Implementation | Coder (per story) | Low | 10K | 8K | $0.05 | $0.13 |
| 4 Review | QA + Security | Medium | 15K | 10K | $0.07 | $0.18 |
| 5 Deploy | DevOps | Low | 8K | 5K | $0.03 | $0.10 |
| 6 Operate | Operator | Low | 5K | 3K | $0.02 | $0.06 |
| **Total per task** | | | **78K** | **54K** | **$0.36** | **$0.97** |

**Note:** Implementation phase multiplied by number of stories. 10 stories = ~$0.50 (Haiku) or ~$1.30 (Sonnet).

## Cost Multipliers

| Factor | Multiplier | Description |
|--------|-----------|-------------|
| Complex architecture | 1.5x | Multi-component systems |
| Security-sensitive | 1.3x | Extra security review cycles |
| Research-heavy | 2.0x | Multiple research iterations |
| Many stories (>20) | 1.2x | Coordination overhead |
| Ponytail optimization | 0.5x | -54% LOC, -22% tokens |
| RTK + Caveman | 0.7x | Compression strategies |
| Cached prompts | 0.1x | Repeated context |

## Monthly Cost Scenarios

### Solo Developer (10 tasks/day)

| Strategy | Model Mix | Est. Monthly |
|----------|-----------|-------------|
| Budget | Haiku + Gemini Flash | $30-90 |
| Standard | Haiku + Sonnet | $95-310 |
| Quality-first | Sonnet + Opus | $300-900 |

### With Optimization (Ponytail + RTK + Caveman)

| Strategy | Model Mix | Est. Monthly | Savings |
|----------|-----------|-------------|---------|
| Budget | Haiku + Gemini Flash | $15-45 | ~50% |
| Standard | Haiku + Sonnet | $50-160 | ~50% |
| Quality-first | Sonnet + Opus | $150-450 | ~50% |

## Cost Tracking

### Per-Task Cost Log

```markdown
# Cost Log: [Task ID]

## Model Usage
| Phase | Model | Input Tokens | Output Tokens | Cost |
|-------|-------|-------------|---------------|------|
| 0 | Haiku | 4,200 | 2,800 | $0.018 |
| 1 | Sonnet | 12,500 | 8,300 | $0.163 |
| ...

## Total
- Tokens: 45,000 input + 30,000 output
- Cost: $0.87
- Optimization applied: Ponytail (-22%), RTK (-15%)
```

### Monthly Dashboard

```markdown
# Monthly Cost Report: [Month]

## Summary
- Total tasks: N
- Total cost: $X.XX
- Average per task: $X.XX
- Model breakdown: Haiku N%, Sonnet N%, Opus N%

## Optimization Impact
- Ponytail savings: $X.XX
- RTK/Caveman savings: $X.XX
- Cache hit savings: $X.XX
```

## Decision Rules

1. **Default to cheapest model that can handle the task**
2. **Escalate to stronger model only when:**
   - Task requires complex reasoning (architecture, security)
   - Cheaper model failed or produced poor quality
   - User explicitly requests higher quality
3. **Always apply Ponytail for coding tasks** (guaranteed -22% token savings)
4. **Use cached prompts for repeated context** (90% input cost reduction)
5. **Track costs per task** to build empirical baseline
