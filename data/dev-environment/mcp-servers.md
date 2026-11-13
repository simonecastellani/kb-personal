---
provenance: [sources/2026-11-13-censimento-server-mcp-opencode-globale-progetto-family-bot-2]
status: in-progress
title: Censimento dei server MCP configurati in OpenCode
type: Reference
---
# Censimento dei server MCP configurati in OpenCode

Aggiornato il 2026-11-13: i server MCP globali stanno passando sotto la gestione di Cartographer come descrittori `mcp/*.json` della KB; i file di configurazione locali non li dichiarano più a mano.

## Dove vivono le configurazioni

| File | Proprietà | Contenuto MCP |
|---|---|---|
| `$HOME/.config/opencode/opencode.json` | globale; a mano restano solo i plugin | `mcp.cartographer`, il bridge che Cartographer riscrive a ogni sync. Nessun altro server |
| `$HOME/opencode.json` | generata da Cartographer, binding `$HOME` → kb-personal | `laya`, `tokensave`, `tinyfish`, materializzati dai descrittori della KB |
| `$HOME/project/family-bot/opencode.json` | progetto family-bot, binding → family-bot-kb | `tokensave`, `deepwiki`, `laminar`, `sonarqube` (off), `codegraph` (off) |

Il vecchio `$HOME/.config/opencode/opencode.jsonc` era un secondo file globale attivo, con plugin e `tinyfish`: il suo contenuto è stato consolidato in `opencode.json` e il file eliminato.

## Server gestiti da Cartographer (kb-personal)

| Server | Descrittore | Trasporto | Materializzato in | Note |
|---|---|---|---|---|
| laya | `mcp/laya.json` | stdio | `$HOME/opencode.json` | `docker run` sulla rete `laya_default`; vedere [[dev-environment/laya-opencode]] |
| tokensave | `mcp/tokensave.json` | stdio | `$HOME/opencode.json` | binario Homebrew con `serve`; il path assoluto è nel descrittore ed è specifico della macchina |
| tinyfish | `mcp/tinyfish.json` | http | `$HOME/opencode.json` | header `X-API-Key: ${TINYFISH_API_KEY}`, che OpenCode traduce in `{env:TINYFISH_API_KEY}` |
| cartographer | nessuno (entry propria) | stdio | `$HOME/.config/opencode/opencode.json` | Cartographer scrive `cartographer mcp --provider opencode` da sé: un descrittore sarebbe circolare |

Ogni descrittore va approvato una volta con `cartographer approve mcp <nome> --kb kb-personal`, legato all'hash del contenuto; una modifica richiede una nuova approvazione.

## Server nativi, fuori da questa KB

`deepwiki`, `laminar`, `sonarqube` e `codegraph` restano nel config del progetto family-bot (perimetro di family-bot-kb). `tokensave` vi compare ancora come override locale del server globale.

## Plugin OpenCode

I plugin non sono un tipo di artefatto Cartographer: restano configurazione nativa del client.

| Plugin | Dove | Cosa fa |
|---|---|---|
| context-mode | globale (`opencode.json`) | gestione del contesto delle sessioni e dei grandi output |
| @lmnr-ai/opencode-plugin | globale (`opencode.json`), forma `{package, options}` | traccia le sessioni in Laminar (self-hosted, gRPC 8001, `baseUrl` sulla rete locale); le opzioni hanno precedenza sulle variabili `LMNR_*` |

Plugin Cartographer installati (`$HOME/.config/opencode/plugins/`): bootstrap e write-findings, dal bundle.

## Credenziali

- **tinyfish**: nessun valore nella KB né nei config. Il descrittore referenzia `${TINYFISH_API_KEY}`, esportata in `$HOME/.zshrc`. OpenCode sostituisce `{env:TINYFISH_API_KEY}` al caricamento; se la variabile manca la sostituzione è la stringa vuota e il server risponde 401, senza errore di configurazione.
- **laminar**: il bearer è ancora in chiaro in `$HOME/project/family-bot/opencode.json` e il `projectApiKey` del plugin in `opencode.json`. Fuori dal perimetro MCP globale: da trattare a parte.

## Nota sulla forma

OpenCode depreca `mcp.<nome>` in favore di `mcp.servers.<nome>`, ma Cartographer (D1172) scrive ancora la forma flat, che lo schema accetta come deprecata. Finché è così, una entry scritta a mano in `mcp.servers` accanto a quella di Cartographer duplica il server: la forma flat è l'unica che sopravvive al sync.

## Quesiti

- [[dev-environment/mcp-config-duplicata]] — risolto per i file globali: un solo file, una sola dichiarazione per server, duplicato `mcp.servers.cartographer` rimosso. Resta il doppio `tokensave` globale/family-bot.
- [[dev-environment/mcp-segreti-in-chiaro]] — risolto per tinyfish; resta laminar.
