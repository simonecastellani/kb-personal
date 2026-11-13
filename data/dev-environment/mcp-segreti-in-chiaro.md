---
contradiction_kind: open_question
involves: [dev-environment/mcp-servers]
resolution: "La API key di tinyfish è uscita dai config: il descrittore usa ${TINYFISH_API_KEY}, risolta da OpenCode come {env:TINYFISH_API_KEY}. Resta laminar, fuori perimetro."
resolution_date: 2026-11-13
resolution_status: resolved
status: done
title: Credenziali MCP in chiaro nelle configurazioni
type: Contradiction
---
# Credenziali MCP in chiaro nelle configurazioni

**Risolto il 2026-11-13** per tinyfish.

Due credenziali erano in chiaro: la `X-API-Key` di tinyfish (`$HOME/.config/opencode/opencode.jsonc`) e il bearer di laminar (`$HOME/project/family-bot/opencode.json`).

Esito:

- **tinyfish**: il valore non è più in nessun config. Il descrittore `mcp/tinyfish.json` dichiara `X-API-Key: ${TINYFISH_API_KEY}`; Cartographer lo traduce in `{env:TINYFISH_API_KEY}` per OpenCode e la variabile è esportata nel profilo zsh. Attenzione: se la variabile manca, OpenCode la sostituisce con la stringa vuota e il server risponde 401 senza errore di configurazione.
- **laminar**: il bearer resta in chiaro in `$HOME/project/family-bot/opencode.json` e il `projectApiKey` del plugin `@lmnr-ai/opencode-plugin` in `opencode.json`. Sono fuori dal perimetro MCP di questa KB.

I valori hanno già circolato in chiaro: la rotazione upstream resta consigliata.

Vedere [[dev-environment/mcp-servers]].
