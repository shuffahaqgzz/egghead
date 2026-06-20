# enowX Labs — Provider Configuration & Integration

_Reference untuk enowX Labs provider setup, API usage, dan integration patterns di Hermes._

---

## Overview

**enowX Labs**: LLM provider dengan inference capabilities untuk custom models, fine-tuning, dan specialized AI workflows.

**Status**: Optional integration. Use when:
- Gilang memiliki custom model di enowX Labs
- Performance/cost trade-off vs Anthropic
- Specialized model inference (fine-tuned, domain-specific)

---

## API Authentication

### Setup Credentials

1. **Get API key** dari enowX Labs dashboard
2. **Store di `~/.hermes/.env`**:
   ```bash
   ENOWXLABS_API_KEY=your-key-here
   ```

3. **Verify setup**:
   ```bash
   echo $ENOWXLABS_API_KEY
   ```

### Environment Variable

```bash
# Check if available
env | grep ENOWX
```

---

## Model Specifications

### Available Models (enowX Labs)

| Model | Context | Cost | Use Case |
|-------|---------|------|----------|
| (TBD) | TBD | TBD | Specify per Gilang config |

_Note: Model list maintained by Gilang / enowX Labs. Update when new models available._

---

## Integration with Hermes

### Router Configuration

If using enowX Labs via 9Router, add to `~/.hermes/config.yaml`:

```yaml
providers:
  enowxlabs:
    api_key: ${ENOWXLABS_API_KEY}
    base_url: "https://api.enowxlabs.com/v1"  # Verify endpoint
    models:
      - enowx-custom-v1
      - enowx-domain-specialist-v2

routing:
  rules:
    - pattern: "specialized_task"
      model: "enowx-domain-specialist-v2"
      provider: "enowxlabs"
```

### Explicit Model Override

For a specific task, force enowX Labs provider:

```python
# In delegate_task or cronjob
{
  "model": {
    "provider": "enowxlabs",
    "model": "enowx-custom-v1"
  }
}
```

---

## Pricing & Cost Control

### Estimate Cost

- **Prompt tokens**: $X per 1M tokens (confirm with enowX Labs)
- **Completion tokens**: $Y per 1M tokens (confirm with enowX Labs)
- **Batch operations**: Potentially lower cost if available

### Cost Monitoring

```bash
# Check recent enowX Labs usage (if API provides usage endpoint)
curl -s -H "Authorization: Bearer $ENOWXLABS_API_KEY" \
  https://api.enowxlabs.com/v1/usage
```

### Budget Limits

Set soft limit to prevent runaway costs:

```bash
# ~/.hermes/.env
ENOWXLABS_MONTHLY_BUDGET=1000  # USD, example
```

Hermes runtime should warn if approaching limit (implementation TBD).

---

## API Endpoints

### Standard Endpoints

| Endpoint | Purpose | Method |
|----------|---------|--------|
| `/v1/models` | List available models | GET |
| `/v1/chat/completions` | Chat inference | POST |
| `/v1/completions` | Text completion | POST |
| `/v1/embeddings` | Embeddings (if supported) | POST |

### Request Example

```bash
curl -X POST https://api.enowxlabs.com/v1/chat/completions \
  -H "Authorization: Bearer $ENOWXLABS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "enowx-custom-v1",
    "messages": [{"role": "user", "content": "Hello"}],
    "max_tokens": 500
  }'
```

---

## Troubleshooting

### Common Errors

| Error | Cause | Fix |
|-------|-------|-----|
| `401 Unauthorized` | Invalid API key | Check `~/.hermes/.env`, regenerate key |
| `429 Too Many Requests` | Rate limited | Backoff, reduce concurrency |
| `503 Service Unavailable` | enowX Labs down | Fallback to Anthropic, check status page |
| `Connection refused` | Wrong endpoint / VPN required | Verify URL, check VPN connection |

### Debug Mode

```bash
# Enable verbose logging for enowX Labs calls
export HERMES_DEBUG_PROVIDERS=enowxlabs
hermes chat -q "test query" --verbose
```

### Test Connectivity

```bash
# Verify API key + endpoint
curl -s -H "Authorization: Bearer $ENOWXLABS_API_KEY" \
  https://api.enowxlabs.com/v1/models | jq .
```

---

## When to Use vs Skip

### Use enowX Labs if:
- Custom model available that outperforms Anthropic Claude
- Cost savings > operational complexity
- Specialized domain performance matters
- Gilang explicitly wants to test / compare

### Skip (use Anthropic default) if:
- API key not setup / missing
- enowX Labs service unavailable
- Latency critical (Anthropic may be faster)
- Task doesn't warrant specialized model

---

## References

- **enowX Labs docs**: https://docs.enowxlabs.com/
- **API Reference**: https://docs.enowxlabs.com/api/
- **Dashboard**: https://dashboard.enowxlabs.com/
- **Support**: support@enowxlabs.com

---

## Notes

- Config maintained by Gilang / enowX Labs account owner
- API key considered sensitive — **never commit to git** or paste in logs
- Integration optional — full Hermes functionality works without it
