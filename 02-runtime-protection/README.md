# Runtime Protection — AIDR

AIDR (AI Detection & Response) analyzes LLM interactions in real time and detects prompt injection, PII leakage, code injection, denial of service, and more.

## Prerequisites

Complete the [top-level setup](../README.md#quick-start) (venv, `pip install`, `.env`) before running this notebook.

## Notebooks

> **⚠️ These notebooks demonstrate two *different* runtime APIs — not two ways of calling the same one.** They use different SDK methods, different request formats, and return the enforcement action in different places. They are **not interchangeable**; pick the one that matches how you intend to integrate.

| Notebook | API version | Description |
|----------|-------------|-------------|
| [`interactions.ipynb`](./interactions.ipynb) | **v1 — Interactions** | Static `client.interactions.analyze()` calls; input + output sent together in HiddenLayer's own schema; action returned in the response **body** (`evaluation.action`) |
| [`request_response_evaluations.ipynb`](./request_response_evaluations.ipynb) | **v2 — Request/Response (pass-through)** | `client.runtime.evaluate_request()` / `evaluate_response()`; native OpenAI/Anthropic payloads scanned pre- and post-LLM; action returned on the `HL-Runtime-Action` response **header** |

Neither notebook instruments a live agent — they call the SDK with hardcoded payloads so the response is the focus.

### v1 vs v2 at a glance

| | v1 — Interactions | v2 — Request/Response |
|---|---|---|
| SDK method | `client.interactions.analyze()` | `client.runtime.evaluate_request()` / `evaluate_response()` |
| Payload format | HiddenLayer schema (`metadata` / `input` / `output`) | Native OpenAI/Anthropic request & response |
| Calls per turn | One (input + output together) | Two (before **and** after the LLM) |
| Where the action is returned | Response **body** (`evaluation.action`) | Response **header** (`HL-Runtime-Action`) |
| Correlation across calls | n/a | `HL-Roundtrip-Id` (links request↔response) + `HL-Runtime-Session-Id` (groups turns) |
| Minimum SDK | any recent | `hiddenlayer-sdk>=3.6.0` |

## SDK Methods Used

The two sections below are **distinct APIs with distinct SDK entry points** (`client.interactions` vs. `client.runtime`), not two options on one endpoint. Note the different request shapes and where each returns its action.

### Interactions API (v1)

```python
response = client.interactions.analyze(
    hl_project_id="Default Project",
    metadata={"model": "gpt-4o", "requester_id": "user-1", "provider": "openai"},
    input={"messages": [{"role": "user", "content": "Hello"}]},
    output={"messages": [{"role": "assistant", "content": "Hi there!"}]},
)
```

Uses HiddenLayer's own format (`metadata`, `input`, `output`). The action is in the response body (`evaluation.action`).

### Request & Response Evaluations (v2)

```python
raw = client.runtime.with_raw_response.evaluate_request(
    body={"model": "gpt-4o", "messages": [{"role": "user", "content": "Hello"}]},
    hl_project_id="Default Project",
    extra_headers={"HL-Roundtrip-Id": "<uuid>", "HL-Runtime-Session-Id": "<session>"},
)
action = raw.headers.get("HL-Runtime-Action", "ALLOW")
payload = raw.json()
```

Uses the **native OpenAI/Anthropic payload** as the body. The action is on the `HL-Runtime-Action` response **header**. Use `with_raw_response` to access both headers and body. Pass `HL-Roundtrip-Id` to link a request eval to its response eval, and `HL-Runtime-Session-Id` to group multiple round-trips.

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
