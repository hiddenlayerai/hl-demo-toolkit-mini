# AGENTS.md

## Project Overview

`hl-demo-toolkit-mini` is a minimal quickstart for the HiddenLayer Python SDK. Each notebook shows the smallest useful call for one product:

- `01-model-scanner/scan.ipynb` — Supply Chain Protection
- `02-runtime-protection/interactions.ipynb` — Runtime Protection (AIDR, v1 interactions)
- `02-runtime-protection/request_response_evaluations.ipynb` — Runtime Protection (v2 pass-through)
- `03-red-team/red_team_eval.ipynb` — AI Attack Simulation

The goal is "what does this SDK call return?" — not realistic agent integration. There is intentionally no shared package, no agent loop, no MCP server, no banking simulation.

## Tech Stack

- **Language:** Python 3.10+
- **SDK:** `hiddenlayer-sdk` (PyPI) — the official HiddenLayer Python SDK
- **SDK repo:** https://github.com/hiddenlayerai/hiddenlayer-sdk-python
- **SDK API reference:** https://github.com/hiddenlayerai/hiddenlayer-sdk-python/blob/main/api.md
- **Format:** Jupyter notebooks (`.ipynb`)
- **Dependencies:** Single top-level `requirements.txt` — `hiddenlayer-sdk`, `python-dotenv`, `openai`

## SDK Authentication

OAuth2 client credentials, read automatically by the SDK from env vars (loaded via `python-dotenv`):

```python
import os
from dotenv import load_dotenv
from hiddenlayer import HiddenLayer

load_dotenv("../.env")

client = HiddenLayer(
    client_id=os.getenv("HIDDENLAYER_CLIENT_ID"),
    client_secret=os.getenv("HIDDENLAYER_CLIENT_SECRET"),
)
```

- Env vars: `HIDDENLAYER_CLIENT_ID`, `HIDDENLAYER_CLIENT_SECRET`
- For async: use `AsyncHiddenLayer` instead of `HiddenLayer`
- For self-hosted: add `base_url="http://host:port"` to the constructor

## Key SDK Methods

### Supply Chain Protection (Model Scanner)
```python
client.model_scanner.scan_file(model_name, model_path, model_version, wait_for_results)
client.community_scanner.community_scan(model_name, model_path, model_source)
```

### Runtime Protection (AIDR) — v1 Interactions
```python
client.interactions.analyze(
    hl_project_id="Default Project",
    metadata={"model": "...", "requester_id": "...", "provider": "..."},
    input={"messages": [{"role": "user", "content": "..."}]},
    output={"messages": [{"role": "assistant", "content": "..."}]},
)
```

### Runtime Protection — v2 Request & Response Evaluations
```python
raw = client.runtime.with_raw_response.evaluate_request(
    body={"model": "gpt-4o", "messages": [...]},
    hl_project_id="Default Project",
    extra_headers={"HL-Roundtrip-Id": "<uuid>", "HL-Runtime-Session-Id": "<session>"},
)
action = raw.headers.get("HL-Runtime-Action", "ALLOW")
payload = raw.json()
```

### AI Attack Simulation (Red Team)
```python
session = await client.evaluation_sessions.red_team.start_session(
    name="...", target_model="...", target_system_prompt="...",
    prompt_set_id=None,
)
results = await session.run_with_callback_parallel(handler=handler)
await client.evaluations.red_team.retrieve_evaluation_results(session.workflow_id)
```

## Coding Conventions

- **Minimal examples** — each notebook shows exactly how to do one thing with minimal wrapping
- **No inline pip installs** — all deps go in the top-level `requirements.txt`
- **No helper libraries** — use the SDK directly, no custom wrappers between notebooks
- **Notebooks are deployment-agnostic** — SaaS by default, self-hosted via `base_url`
- **Each directory is self-contained** — own README, own notebook, references top-level setup
- **Print raw SDK responses** — use `print(response.model_dump_json(indent=2))` for full JSON or targeted print statements for specific fields
- **Comments only for labeling** — e.g., `# Safe — benign question` or `# Detection — prompt injection attempt`

## Commit Hygiene

- Never add "Co-authored-by", "Made-with", "Generated-by", or any trailer/tag attributing a coding agent or AI tool to commit messages
- Keep commit messages concise and focused on the change itself

## Files to Never Commit

- `.env` — contains real API credentials
- Any file with real customer data or credentials

## Product Documentation

- [HiddenLayer Documentation](https://docs.hiddenlayer.ai/)
- [Developer Portal](https://dev.hiddenlayer.ai/)
- [Python SDK API Reference](https://github.com/hiddenlayerai/hiddenlayer-sdk-python/blob/main/api.md)
