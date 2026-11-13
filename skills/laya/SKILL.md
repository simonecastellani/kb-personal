---
name: laya
description: Use the local Laya decision engine (Docker, exposed via the "laya" MCP server) for cheap typed decisions over text — classification, routing, triage, scoring, yes/no questions — without spending main-model tokens. Use it whenever a task needs many small decisions over text, or a fast local signal instead of an LLM call.
version: "1.0"
---
# Laya — local decision engine

Laya is a small (421M) local decision model. It does **not** generate text: given a
`state` (the text to judge) and typed `questions`, it answers in milliseconds on the
local Docker server. Use it to offload decisions that would otherwise cost a full
LLM call, and to keep those micro-decisions out of the main model's context.

## When to use it

| Situation | Example |
|---|---|
| Classifying / triaging text | bug vs feature vs question; which team owns an issue |
| Routing decisions | which subagent or model should handle this task |
| Scoring | urgency 0–2, priority, risk level |
| Yes/no judgments | "is this a breaking change?", "does this report mention auth?" |
| Many decisions at once | `laya_predict_batch` (up to 64 states, one call) |
| Confidence gating | decide whether an output needs re-checking before showing it |

Do **not** use Laya for anything that needs generation, reasoning chains, or code
understanding — those belong to the main model. Laya is a signal, not an oracle:
its zero-shot confidence can be over-confident; treat `confidence` as a signal to
act on, not a guarantee.

## How to call it

One forward pass answers **all** questions on a state. Question types:

- `choice` — pick one option from `criteria` (option name → description)
- `score` — ordinal scale; add `legend` (e.g. `{"0": "not urgent", "2": "critical"}`)
- `noul` — yes/no; the answer is the probability of yes

```json
{
  "state": {"body": "user text to judge"},
  "questions": {
    "team":  {"type": "choice", "instructions": "which team?",
              "criteria": {"billing": "refunds and payments", "tech": "bugs"}},
    "urgent": {"type": "noul", "instructions": "Is this urgent?"}
  }
}
```

## Tools

| Tool | Use |
|---|---|
| `laya_predict` | One state, one set of questions |
| `laya_predict_batch` | Many `{state, questions}` requests in one call |
| `laya_decide` | Answer a JSON-schema-shaped decision (enums, booleans, bounded ints) with per-field confidence |
| `laya_preset` | Built-in workflow question sets |
| `laya_shortlist` | A choice question with many options (shortlists first) |
| `laya_route` / `laya_route_batch` | See which checkpoint would answer, no inference |
| `laya_status` | Server health, loaded checkpoints, versions |

Notes for this setup (shared-server / remote mode):

- `laya_predict` over HTTP reads only the **first window** of a very long state;
  `laya_predict_long` and `laya_tournament` are in-process only and not available here.
- Keep `state` text concise (paste the relevant text, not whole files).
- `model` can be omitted (auto) or set to `english`, `multilingual`, `typed-decisions`.

## Operations (if the server is down)

- Server: `docker compose -f compose.yaml -f compose.http.yaml up -d laya-serve`
  in `$HOME/tools/laya` (binds `127.0.0.1:8000`, `restart: unless-stopped`).
- Health check: `curl http://127.0.0.1:8000/health`.
- MCP proxy image `laya-mcp:latest` is built from `$HOME/.local/share/laya-mcp/Dockerfile`
  (a thin `laya[mcp]` layer over `laya-laya-serve:latest`); rebuild after a
  `docker compose build` changes the base image.
- The `laya` server is a Cartographer artifact: its descriptor is `mcp/laya.json` in
  kb-personal and is materialized into the workspace's `opencode.json`. The descriptor
  MUST pass `-e LAYA_BASE_URL=http://laya-serve:8000` in the docker args: the config's
  `environment` field only reaches the docker CLI process, not the container. Without
  it the MCP server starts in local mode and silently downloads checkpoints instead of
  proxying, and connections time out.
- If `laya_status` fails, restart the server with the command above before retrying.
