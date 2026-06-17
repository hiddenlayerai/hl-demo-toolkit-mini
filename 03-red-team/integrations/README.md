# Red Team Integrations

Notebooks showing how to run a HiddenLayer red team evaluation against different
kinds of targets. They build on the basic [`red_team_eval.ipynb`](../red_team_eval.ipynb)
(OpenAI target) and vary only in **how the `handler` reaches the target**.

Each notebook follows the same flow:

1. **Configurables** — target + evaluation settings.
2. **Handler** — an async function that calls the target and returns its reply.
3. **Run** — `hl_client.evaluation_sessions.red_team.start_session(...)` then `session.run_with_callback_parallel(handler=handler)`.
4. **Retrieve Results** — summarize the evaluation report.

## Notebooks

| Notebook | Target |
|----------|--------|
| [`http_api.ipynb`](./http_api.ipynb) | A custom LLM app/agent behind an HTTP/REST endpoint |
| [`playwright.ipynb`](./playwright.ipynb) | A web chat UI driven via Playwright browser automation |
| [`static_prompts.ipynb`](./static_prompts.ipynb) | A target tested with a pre-written static prompt set |
| [`template.ipynb`](./template.ipynb) | Generic skeleton — drop in any target |

For a direct OpenAI target, see [`../red_team_eval.ipynb`](../red_team_eval.ipynb).

## Setup

These notebooks reuse the toolkit's root setup:

```bash
pip install -r ../../requirements.txt
```

Credentials are read from `../../.env`:

- `HIDDENLAYER_CLIENT_ID`
- `HIDDENLAYER_CLIENT_SECRET`

`playwright.ipynb` needs the extra browser dependency:

```bash
pip install playwright
playwright install chromium
```

Results are viewable in the [HiddenLayer Console](https://console.hiddenlayer.ai/).
