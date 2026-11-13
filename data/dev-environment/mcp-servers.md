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
- `$HOME/opencode.json` — config di **workspace** generata da Cartographer (binding `$HOME` → kb-personal): vi è materializzato il server MCP `laya`

## Server

| Server | Tipo | Dove | Avvio / URL | Stato | Scopo |
|---|---|---|---|---|---|
| tokensave | local | globale | `tokensave serve` (brew) | attivo | code graph MCP |
| cartographer | local | globale | `cartographer mcp --provider opencode` (brew) | attivo | server KB (Cartographer) |
| laya | local | workspace `$HOME` — artefatto `mcp/laya.json` di kb-personal | `docker run … laya-mcp:latest` con `-e LAYA_BASE_URL` | attivo | motore decisionale — vedere [[dev-environment/laya-opencode]] |
| tinyfish | remote | globale (jsonc) | `https://agent.tinyfish.ai/mcp` | attivo | web search/fetch/automation (header API key) |
| tokensave | local | progetto family-bot | `tokensave serve` | attivo | duplicato del globale |
| deepwiki | remote | progetto family-bot | `https://mcp.deepwiki.com/mcp` | attivo | wiki di repository |
| laminar | remote | progetto family-bot | `http://192.168.178.204:8000/v1/mcp` | attivo | osservabilità LLM (header Bearer) |
| sonarqube | local | progetto family-bot | `docker run … mcp/sonarqube` | disabilitato | analisi SonarQube; richiede `SONARQUBE_URL`/`SONARQUBE_ORG` |
| codegraph | local | progetto family-bot | `codegraph serve --mcp` | disabilitato | code graph alternativo, `codemode: false` |

## Plugin OpenCode

I plugin OpenCode si dichiarano nel campo `plugins` dei config del client e **non sono un tipo di artefatto Cartographer**: restano configurazione nativa del client (i plugin di Cartographer — cartelle con `cartographer-plugin.yaml` — sono un'altra cosa).

| Plugin | Dove | Cosa fa |
|---|---|---|
| context-mode | globale (`$HOME/.config/opencode/opencode.jsonc`) | gestione del contesto delle sessioni e dei grandi output |
| @lmnr-ai/opencode-plugin | globale (`$HOME/.config/opencode/opencode.jsonc`), in forma `{package, options}` con `projectApiKey` e `baseUrl` | plugin Laminar: traccia le sessioni OpenCode in Laminar (un trace per turno, con chiamate LLM e tool); complementare al server MCP `laminar`. Base URL: `http://192.168.178.204` (istanza self-hosted, gRPC 8001, come da `.env` del progetto family-bot); le opzioni del plugin hanno precedenza sulle variabili `LMNR_*` |

Plugin Cartographer installati (in `$HOME/.config/opencode/plugins/`): bootstrap e write-findings, dal bundle.

Nota: `@lmnr-ai/opencode-plugin` è nella cache plugin di OpenCode (`$HOME/.cache/opencode/npm/@lmnr-ai/opencode-plugin@latest`), non in `node_modules` del progetto.

## Server gestiti da Cartographer

`laya` è il primo server MCP gestito come artefatto: il descrittore vive nella KB (`mcp/laya.json`), si approva una volta con `cartographer approve mcp laya --kb kb-personal` (legato all'hash del contenuto) e `cartographer sync` lo materializza nel config del workspace che serve la KB. La entry manuale che stava nel config globale è stata rimossa: Cartographer è l'unico proprietario della configurazione.

## Credenziali

Due server usano header di autenticazione (tinyfish `X-API-Key`, laminar `Authorization: Bearer`). I valori sono deliberatamente non registrati qui: sono in chiaro nelle rispettive configurazioni. Vedere [[dev-environment/mcp-segreti-in-chiaro]].

## Quesiti aperti

- [[dev-environment/mcp-config-duplicata]] — tokensave duplicato, `opencode.json` + `opencode.jsonc`, entry legacy `mcp.cartographer`.
- [[dev-environment/mcp-segreti-in-chiaro]] — spostare le due credenziali in env/SOPS.
