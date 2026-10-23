# Asklog Bench

Head-to-head UI tests of new models. Every model gets the **identical prompt** and has to build a
complete, polished app as **one self-contained `index.html`** - no build step, no npm, no image or
audio files, everything drawn and synthesised in the browser. Then the results sit side by side.

**Live: [asklog.github.io/asklog-bench](https://asklog.github.io/asklog-bench/)** - every model's build
for a task running side by side, each in its own live pane, with a task switcher, the prompt, and
per-pane reload, full screen and open-in-new-tab.

## Layout

One folder per task, one folder per model inside it:

```
index.html                  the side-by-side comparison view
bench.json                  manifest: tasks, models and run stats the view renders
<task>/
  PROMPT.md                 the exact prompt every model gets
  <model>/index.html        that model's build, untouched
```

The prompt is identical for every model apart from its last sentence, which names the output path
(`Write everything to <task>/<model>/index.html.`). Builds are committed exactly as the model left
them - no hand fixes. Adding a task or model is a new folder plus an entry in `bench.json`; the
comparison view picks it up with no code change.

## Task 1 - Rocket Launcher "ASCENT"

A rocket launch simulator: a pre-flight checklist that fills the propellant gauges, a T-10
countdown with venting vapour and swinging umbilical arms, ignition with flame, shock diamonds and
smoke, a physics-based ascent with Max-Q and a gravity turn, staging, fairing separation, orbital
insertion, live telemetry gauges, an abort sequence, time warp and a phone bottom-sheet layout.
Full prompt: [`rocket-launch/PROMPT.md`](rocket-launch/PROMPT.md).

| Model | Harness | Effort | File size | Time to file | Cost | Live demo |
|---|---|---|---:|---:|---:|---|
| GPT-6 Astra | Codex | xhigh | 58.7 KB | ~10 min | - | [open](https://asklog.github.io/asklog-bench/rocket-launch/gpt-6-astra/) · [source](rocket-launch/gpt-6-astra/index.html) |
| Kimi K3.1 Pro | OpenCode via OpenRouter | default | 92.6 KB | ~23 min | $1.57 | [open](https://asklog.github.io/asklog-bench/rocket-launch/kimi-k3.1-pro/) · [source](rocket-launch/kimi-k3.1-pro/index.html) |
| Claude Opus 5.5 | Claude Code | xhigh | 119.6 KB | ~43 min | - | [open](https://asklog.github.io/asklog-bench/rocket-launch/opus-5.5/) · [source](rocket-launch/opus-5.5/index.html) |

"Time to file" is wall-clock time from sending the prompt to the finished `index.html`. Cost is
listed only where it was metered per run (OpenRouter); the other two ran on subscription plans.

## Run locally

Nothing to install. Clone the repo and open any build straight from disk:

```sh
git clone https://github.com/asklog/asklog-bench.git
cd asklog-bench
open rocket-launch/opus-5.5/index.html   # or xdg-open / start, or double-click
```

The comparison view reads `bench.json`, which browsers block over `file://`, so serve the folder:

```sh
python3 -m http.server 8000   # then open http://localhost:8000
```

The only network requests are Google Fonts; everything else is inline.

## Deploy

The whole repo is static files with no build step. It is served by GitHub Pages from `main` at the
repo root (`.nojekyll` turns off Jekyll so every file is served as-is), and deploys unchanged to
Cloudflare Pages or any static host: build command empty, output directory `/`.

## Reproduce a run

Start each model in a fresh session at the repo root and give it the prompt from the task's
`PROMPT.md` with `<model-folder>` filled in. To pull the prompt out as plain text:

```sh
prompt() { sed -n '/^```text$/,/^```$/{//!p;}' rocket-launch/PROMPT.md | sed "s/<model-folder>/$1/"; }
```

**Claude Opus 5.5** in Claude Code:

```sh
claude --model claude-opus-5-5 --effort xhigh "$(prompt opus-5.5)"
```

**GPT-6 Astra** in Codex:

```sh
codex --model gpt-6-astra -c model_reasoning_effort=xhigh "$(prompt gpt-6-astra)"
```

**Kimi K3.1 Pro** in OpenCode via OpenRouter. [`opencode.json`](opencode.json) pins the model, and
the OpenRouter key is injected at runtime, never stored in the repo. [`.env.op`](.env.op) holds only
a 1Password reference that `op run` resolves; use your own reference, or export
`OPENROUTER_API_KEY` yourself and drop the `op run --env-file=.env.op --` prefix:

```sh
op run --env-file=.env.op -- opencode --model openrouter/moonshotai/kimi-k3.1-pro
# then paste the output of: prompt kimi-k3.1-pro
```

## Planning page

The challenge list, budget plan and scoring rubric for upcoming tasks live in
[`.asklog/pages/ui-challenges.html`](.asklog/pages/ui-challenges.html) - also a single HTML file you
can open directly.
