---
involves: [dev-environment/mcp-servers]
resolution: "File globali consolidati in opencode.json, duplicato mcp.servers.cartographer rimosso, tokensave e tinyfish spostati nei descrittori mcp/ della KB."
resolution_date: 2026-11-13
resolution_status: resolved
status: done
title: Configurazione MCP duplicata tra file e progetti
type: Contradiction
contradiction_kind: open_question
---
# Configurazione MCP duplicata tra file e progetti

**Risolto il 2026-11-13** per la parte globale.

I tre problemi osservati nel censimento:

1. `tokensave` dichiarato sia in `$HOME/.config/opencode/opencode.json` sia in `$HOME/project/family-bot/opencode.json`.
2. Due file globali, `opencode.json` e `opencode.jsonc`, entrambi caricati.
3. Entry legacy `mcp.cartographer` fuori da `mcp.servers`.

Esito:

- I due file globali sono stati consolidati in `opencode.json`; `opencode.jsonc` è stato eliminato.
- La entry `mcp.servers.cartographer` era il duplicato: Cartographer riscrive la propria entry in forma flat `mcp.cartographer` a ogni sync (verificato svuotando `mcp` e rilanciando `cartographer sync`).
- `tokensave` e `tinyfish` non sono più dichiarati a mano: vivono nei descrittori `mcp/tokensave.json` e `mcp/tinyfish.json` e vengono materializzati in `$HOME/opencode.json`.

Il residuo del punto 1 (il `tokensave` locale in `$HOME/project/family-bot/opencode.json`) sta nel perimetro di family-bot-kb, fuori da questa KB.

Vedere [[dev-environment/mcp-servers]].
