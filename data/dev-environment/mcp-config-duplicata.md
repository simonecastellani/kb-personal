---
title: Configurazione MCP duplicata tra file e progetti
type: Contradiction
contradiction_kind: open_question
involves: [dev-environment/mcp-servers]
status: open
---
# Configurazione MCP duplicata tra file e progetti

Tre forme di duplicazione osservate nel censimento del 2026-11-13:

1. `tokensave` è dichiarato sia in `$HOME/.config/opencode/opencode.json` sia in `$HOME/project/family-bot/opencode.json`.
2. Esistono due file globali, `opencode.json` e `opencode.jsonc`, entrambi caricati.
3. In `opencode.json` resta una entry legacy `mcp.cartographer` fuori da `mcp.servers` (forma V1).

Da decidere: consolidare su un solo file globale e una sola dichiarazione per server, ed eliminare la entry legacy.
