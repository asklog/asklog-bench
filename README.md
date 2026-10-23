# model-testing

A sandbox for trying new models by having them build small HTML apps, to test their UI ability.

## Models

| Model | Harness | Folder name |
|---|---|---|
| Kimi K3.1 Pro (OpenRouter id: `moonshotai/kimi-k3.1-pro`) | OpenCode | `kimi-k3.1-pro` |
| Claude Opus 5.5 | Claude Code (fresh session) | `opus-5.5` |
| GPT-6 Astra | Codex | `gpt-6-astra` |

## Layout

One folder per task at the repo root, and inside it one folder per model:

```
<task>/
  PROMPT.md               the exact prompt every model gets
  <model-folder>/index.html
```

For example `rocket-launch/opus-5.5/index.html` and `rocket-launch/kimi-k3.1-pro/index.html`.
Prompts are model-neutral apart from their last sentence,
`Write everything to <task>/<model-folder>/index.html.`

The challenge list, budget plan, spend tracker and scoring rubric live in the asklog page
`.asklog/pages/ui-challenges.html` (`asklog pages open .asklog/pages/ui-challenges.html`).

## Running

Run Kimi with `op run --env-file=.env.op -- opencode` (injects the OpenRouter key from 1Password).
