---
provenance: [sources/2026-11-13-censimento-server-mcp-opencode-globale-progetto-family-bot-2]
status: stable
title: Censimento dei server MCP configurati in OpenCode
type: Reference
---
# Censimento dei server MCP configurati in OpenCode

Censimento del 2026-11-13 di tutti i server MCP configurati per OpenCode sul client.

## Dove vivono le configurazioni

- `$HOME/.config/opencode/opencode.json` — globale
- `$HOME/.config/opencode/opencode.jsonc` — globale (entrambi i file sono attivi: i server di entrambi risultano connessi)
- `$HOME/project/family-bot/opencode.json` — progetto family-bot

## Server

| Server | Tipo | Dove | Avvio / URL | Stato | Scopo |
|---|---|---|---|---|---|
| tokensave | local | globale | `tokensave serve` (brew) | attivo | code graph MCP |
| cartographer | local | globale | `cartographer mcp --provider opencode` (brew) | attivo | server KB (Cartographer) |
| laya | local | globale | `docker run … laya-mcp:latest` con `-e LAYA_BASE_URL` | attivo | motore decisionale — vedere [[dev-environment/laya-opencode]] |
| tinyfish | remote | globale (jsonc) | `https://agent.tinyfish.ai/mcp` | attivo | web search/fetch/automation (header API key) |
| tokensave | local | progetto family-bot | `tokensave serve` | attivo | duplicato del globale |
| deepwiki | remote | progetto family-bot | `https://mcp.deepwiki.com/mcp` | attivo | wiki di repository |
| laminar | remote | progetto family-bot | `http://192.168.178.204:8000/v1/mcp` | attivo | osservabilità LLM (header Bearer) |
| sonarqube | local | progetto family-bot | `docker run … mcp/sonarqube` | disabilitato | analisi SonarQube; richiede `SONARQUBE_URL`/`SONARQUBE_ORG` |
| codegraph | local | progetto family-bot | `codegraph serve --mcp` | disabilitato | code graph alternativo, `codemode: false` |

## Plugin (contesto)

- Globali: `context-mode` (dichiarato in `opencode.jsonc`); `$HOME/.config/opencode/plugins/` contiene i plugin cartographer (bootstrap, write-findings).
- Progetto family-bot: `@lmnr-ai/opencode-plugin`.

## Credenziali

Due server usano header di autenticazione (tinyfish `X-API-Key`, laminar `Authorization: Bearer`). I valori sono deliberatamente non registrati qui: sono in chiaro nelle rispettive configurazioni. Vedere [[dev-environment/mcp-segreti-in-chiaro]].

## Quesiti aperti

- [[dev-environment/mcp-config-duplicata]] — tokensave duplicato, `opencode.json` + `opencode.jsonc`, entry legacy `mcp.cartographer`.
- [[dev-environment/mcp-segreti-in-chiaro]] — spostare le due credenziali in env/SOPS.
