# Runtime Protection — AIDR

AIDR (AI Detection & Response) analyzes LLM interactions in real time and detects prompt injection, PII leakage, code injection, denial of service, and more.

## Prerequisites

Complete the [top-level setup](../README.md#quick-start) (venv, `pip install`, `.env`) before running this notebook.

## Notebook

| Notebook | Description |
|----------|-------------|
| [`interactions.ipynb`](./interactions.ipynb) | Static `interactions.analyze()` calls demonstrating each detection type |

The notebook does **not** instrument a live agent — it calls `analyze()` with hardcoded input/output pairs so the SDK response is the focus. For agent-instrumentation patterns (input/output gates, per-tool-call scanning, MCP), see the full POV toolkit.

## SDK Method Used

```python
import hiddenlayer

client = hiddenlayer.HiddenLayer(client_id="...", client_secret="...")

response = client.interactions.analyze(
    hl_project_id="Default Project",
    metadata={"model": "gpt-4o", "requester_id": "user-1", "provider": "openai"},
    input={"messages": [{"role": "user", "content": "Hello"}]},
    output={"messages": [{"role": "assistant", "content": "Hi there!"}]},
)
```

The `hl_project_id` parameter maps to the `HL-Project-Id` header and controls which ruleset/policy governs detection. Without it, detections may not be evaluated.

## Detection Capabilities

| Detection | Phase | Description |
|-----------|-------|-------------|
| Prompt Injection | Input | Detects attempts to hijack LLM behavior |
| PII Detection | Input & Output | Identifies personally identifiable information |
| Code Detection | Input & Output | Detects code in messages |
| Denial of Service | Input | Identifies prompts exceeding token thresholds |
| Guardrail Detection | Output | Detects LLM refusals and guardrail activations |
| URL Detection | Input & Output | Identifies URLs in messages |
| Language Detection | Input | Detects disallowed languages |
| Block List | Input | Matches against configured block lists |

## Response Structure

Each `analyze()` response includes:
- `evaluation.action` — `Allow`, `Alert`, `Redact`, or `Block`
- `evaluation.threat_level` — `None`, `Low`, `Medium`, `High`, `Critical`
- `analysis[]` — per-analyzer results with detection status and findings
- `modified_data` — input/output after any redactions applied

## Resources

- [AIDR Documentation](https://docs.hiddenlayer.ai/docs/products/runtime/overview)
- [Interactions API](https://docs.hiddenlayer.ai/docs/products/runtime/interactions)
- [Python SDK](https://github.com/hiddenlayerai/hiddenlayer-sdk-python)
