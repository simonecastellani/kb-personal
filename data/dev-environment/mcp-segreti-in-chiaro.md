---
status: open
title: Credenziali MCP in chiaro nelle configurazioni
type: Contradiction
contradiction_kind: open_question
involves: [dev-environment/mcp-servers]
---
# Credenziali MCP in chiaro nelle configurazioni

Due credenziali sono scritte in chiaro nei file di configurazione OpenCode: la `X-API-Key` di tinyfish (`$HOME/.config/opencode/opencode.jsonc`) e il bearer token di laminar (`$HOME/project/family-bot/opencode.json`).

Da fare: spostarle in variabili d'ambiente con sostituzione `{env:NOME}` (o in SOPS via `secret_set`) e ruotare i valori esposti, dato che sono stati letti e potenzialmente condivisi in questa sessione.
