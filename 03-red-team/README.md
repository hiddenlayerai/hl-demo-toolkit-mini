# AI Attack Simulation — Red Team

Run automated red team evaluations against an LLM target using the HiddenLayer Python SDK. HiddenLayer orchestrates the attack techniques; your `handler` function calls the target and returns its reply.

## Prerequisites

Complete the [top-level setup](../README.md#quick-start) (venv, `pip install`, `.env`) before running this notebook. This notebook also requires `OPENAI_API_KEY` in `.env` (OpenAI is used as the example target).

## Notebook

| Notebook | Description |
|----------|-------------|
| [`red_team_eval.ipynb`](./red_team_eval.ipynb) | Start a red team session against a plain OpenAI chat target and retrieve results |

The target in this notebook is a bare `openai.AsyncOpenAI` chat call — no tools, no agent loop. The point is to show the smallest possible handler that satisfies the red team API contract.

## How It Works

1. You define a `handler(prompt, history, session_id, target_system_prompt)` function that takes an attack prompt and returns the target's response.
2. `evaluation_sessions.red_team.start_session(...)` opens a session and returns a workflow ID.
3. `session.run_with_callback_parallel(handler=handler)` runs the attack techniques in parallel, calling your handler for each prompt.
4. `evaluations.red_team.retrieve_evaluation_results(workflow_id)` returns the full report. Detailed transcripts are also viewable in the [HiddenLayer Console](https://console.hiddenlayer.ai/).

## SDK Methods Used

```python
import hiddenlayer

hl_client = hiddenlayer.AsyncHiddenLayer(client_id="...", client_secret="...")

session = await hl_client.evaluation_sessions.red_team.start_session(
    name="My Evaluation",
    target_model="gpt-4o-mini",
    target_system_prompt="You are a helpful assistant.",
    prompt_set_id=None,
)

results = await session.run_with_callback_parallel(handler=handler)

report = await hl_client.evaluations.red_team.retrieve_evaluation_results(
    session.workflow_id
)
```

## Resources

- [Attack Simulation Docs](https://docs.hiddenlayer.ai/docs/products/ai-attack-simulation)
- [Python SDK](https://github.com/hiddenlayerai/hiddenlayer-sdk-python)
