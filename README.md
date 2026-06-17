# HiddenLayer Demo Toolkit (mini)

A minimal, three-notebook quickstart for the [HiddenLayer Python SDK](https://github.com/hiddenlayerai/hiddenlayer-sdk-python). Each notebook is self-contained and shows the basic SDK call for one product.

## Products

| Notebook | Product | Shows |
|----------|---------|-------|
| [`01-model-scanner/scan.ipynb`](./01-model-scanner/scan.ipynb) | **Supply Chain Protection** | Scan a local model file and a HuggingFace community model |
| [`02-runtime-protection/interactions.ipynb`](./02-runtime-protection/interactions.ipynb) | **Runtime Protection (AIDR)** | Analyze LLM interactions for prompt injection, PII, code, DoS, and refusals |
| [`02-runtime-protection/request_response_evaluations.ipynb`](./02-runtime-protection/request_response_evaluations.ipynb) | **Runtime Protection (v2)** | Evaluate raw OpenAI request/response payloads inline with the v2 pass-through API |
| [`03-red-team/red_team_eval.ipynb`](./03-red-team/red_team_eval.ipynb) | **AI Attack Simulation** | Run a red team evaluation against an OpenAI target |

## Red Team Integrations

The [`03-red-team/integrations/`](./03-red-team/integrations) folder shows how to run red team evaluations against other kinds of targets by swapping the `handler`:

| Notebook | Target |
|----------|--------|
| [`03-red-team/integrations/http_api.ipynb`](./03-red-team/integrations/http_api.ipynb) | A custom LLM app/agent behind an HTTP/REST endpoint |
| [`03-red-team/integrations/playwright.ipynb`](./03-red-team/integrations/playwright.ipynb) | A web chat UI driven via Playwright browser automation |
| [`03-red-team/integrations/static_prompts.ipynb`](./03-red-team/integrations/static_prompts.ipynb) | A target tested with a pre-written static prompt set |
| [`03-red-team/integrations/template.ipynb`](./03-red-team/integrations/template.ipynb) | Generic skeleton — drop in any target |

## Quick Start

```bash
git clone <this-repo>
cd hl-demo-toolkit-mini

python -m venv .venv
source .venv/bin/activate    # macOS/Linux
# .venv\Scripts\activate     # Windows

pip install -r requirements.txt
cp .env.example .env
```

Edit `.env` with your HiddenLayer API credentials. Add `OPENAI_API_KEY` if you plan to run the red team notebook.

## Credentials

Get your API credentials from [HiddenLayer Console](https://console.hiddenlayer.ai) → Settings → API Keys.

| Variable | Used by | Description |
|----------|---------|-------------|
| `HIDDENLAYER_CLIENT_ID` | All notebooks | HiddenLayer API client ID |
| `HIDDENLAYER_CLIENT_SECRET` | All notebooks | HiddenLayer API client secret |
| `OPENAI_API_KEY` | `03-red-team` only | OpenAI key used as the red team target |

## Self-hosted

All notebooks default to HiddenLayer SaaS. For a self-hosted deployment, pass `base_url="http://your-host:port"` to the `HiddenLayer()` / `AsyncHiddenLayer()` constructor.

## Resources

- [HiddenLayer Documentation](https://docs.hiddenlayer.ai/)
- [HiddenLayer Console](https://console.hiddenlayer.ai/)
- [Python SDK](https://github.com/hiddenlayerai/hiddenlayer-sdk-python)
- [SDK API Reference](https://github.com/hiddenlayerai/hiddenlayer-sdk-python/blob/main/api.md)
- [Developer Portal](https://dev.hiddenlayer.ai/)
