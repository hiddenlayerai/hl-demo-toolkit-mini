# AI Attack Simulation — Red Team

Run automated red team evaluations against an LLM target using the HiddenLayer Python SDK. HiddenLayer orchestrates the attack techniques; your `handler` function calls the target and returns its reply.

## Prerequisites

Complete the [top-level setup](../README.md#quick-start) (venv, `pip install -r requirements.txt`, `.env`) before running these notebooks. `red_team_openai.ipynb` also requires `OPENAI_API_KEY` in `.env` (OpenAI is the example target).

## Notebooks

Every notebook follows the same flow — configure the target, implement an async `handler`, start a session, run attacks in parallel, optionally resume, then retrieve results — and varies only in **how the `handler` reaches the target**.

| Notebook | Target |
|----------|--------|
| [`red_team_openai.ipynb`](./red_team_openai.ipynb) | An OpenAI Chat Completions model (the smallest possible handler) |
| [`red_team_http_api.ipynb`](./red_team_http_api.ipynb) | A custom LLM app/agent behind an HTTP/REST endpoint |
| [`red_team_static_prompts.ipynb`](./red_team_static_prompts.ipynb) | A target tested with a pre-written static prompt set |
| [`red_team_playwright.ipynb`](./red_team_playwright.ipynb) | A web chat UI driven via Playwright browser automation |
| [`red_team_webhook.ipynb`](./red_team_webhook.ipynb) | A target that replies asynchronously via webhook callback (self-contained mock) |
| [`red_team_websocket.ipynb`](./red_team_websocket.ipynb) | A target reached over a persistent WebSocket connection (self-contained mock) |
| [`red_team_template.ipynb`](./red_team_template.ipynb) | Generic skeleton — drop in any target |

## Per-notebook extras

- `red_team_openai.ipynb` — `OPENAI_API_KEY` in `../.env`
- `red_team_playwright.ipynb` — `pip install playwright && playwright install chromium`
- `red_team_webhook.ipynb`, `red_team_websocket.ipynb` — `aiohttp` (already in `requirements.txt`); for a real external webhook target, expose the local receiver with a tunnel (e.g. ngrok / cloudflared) and set `PUBLIC_CALLBACK_URL`

## How It Works

1. You define a `handler(prompt, history, session_id, target_system_prompt)` function that takes an attack prompt and returns the target's response.
2. `evaluation_sessions.red_team.start_session(...)` opens a session and returns a workflow ID.
3. `session.run_with_callback_parallel(handler=handler)` runs the attack techniques in parallel, calling your handler for each prompt.
4. `evaluation_sessions.red_team.resume_session(...)` + `evaluations.red_team.retrieve_status(...)` let you reconnect to and monitor an existing workflow.
5. `evaluations.red_team.retrieve_evaluation_results(workflow_id)` returns the full report. Detailed transcripts are also viewable in the [HiddenLayer Console](https://console.hiddenlayer.ai/).

## SDK Methods Used

```python
import hiddenlayer

hl_client = hiddenlayer.AsyncHiddenLayer(client_id="...", client_secret="...")

session = await hl_client.evaluation_sessions.red_team.start_session(
    name="My Evaluation",
    target_model="gpt-4o-mini",
    target_system_prompt="You are a helpful assistant.",
)

await session.run_with_callback_parallel(handler=handler)

report = await hl_client.evaluations.red_team.retrieve_evaluation_results(
    session.workflow_id
)
```

## Resources

- [Attack Simulation Docs](https://docs.hiddenlayer.ai/docs/products/ai-attack-simulation)
- [Python SDK](https://github.com/hiddenlayerai/hiddenlayer-sdk-python)
