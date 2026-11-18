# Log

## 2026-11-18T15:22:18Z

concept_batch (1 operation(s)):
- dev-environment/mcp-servers

## 2026-11-14T15:40:00Z

concept_patch: dev-environment/mcp-servers

## 2026-11-13T20:55:32Z

[dev-environment] 2026-11-13 — Refactor delle configurazioni MCP globali di OpenCode. - Creati i descrittori `mcp/tokensave.json` (stdio) e `mcp/tinyfish.json` (http, `X-API-Key: ${TINYFISH_API_KEY}`), approvati con `cartographer approve mcp … --yes` e sincronizzati. - Verificato che Cartographer scrive la propria entry in forma flat `mcp.cartographer` a ogni sync: la copia in `mcp.servers.cartographer` era il duplicato ed è stata rimossa. - Consolidati i due file globali in `$HOME/.config/opencode/opencode.json` (solo plugin + entry di Cartographer); `opencode.jsonc` eliminato. Backup dei file originali in `/tmp/opencode/mcp-backup/`. - `$HOME/opencode.json` ora materializza `laya`, `tokensave`, `tinyfish`; i tool di tokensave e tinyfish rispondono con `{env:TINYFISH_API_KEY}`. - Chiusi [[dev-environment/mcp-config-duplicata]] e [[dev-environment/mcp-segreti-in-chiaro]] per la parte globale. Restano: doppio `tokensave` in `$HOME/project/family-bot/opencode.json`, bearer laminar in chiaro, `projectApiKey` del plugin lmnr in chiaro. - Nota di prodotto: OpenCode depreca `mcp.<nome>` in favore di `mcp.servers.<nome>`, ma Cartographer emette ancora la forma flat (D1172).

## 2026-11-13T20:54:56Z

concept_patch: dev-environment/mcp-servers

## 2026-11-13T18:00:53Z

concept_batch (2 operation(s)):
- dev-environment/mcp-config-duplicata
- dev-environment/mcp-segreti-in-chiaro

## 2026-11-13T18:00:18Z

concept_write: dev-environment/mcp-servers

## 2026-11-13T17:02:33Z

Plugin Laminar @lmnr-ai/opencode-plugin spostato in globale: opencode.jsonc con forma {package, options} (projectApiKey + baseUrl http://192.168.178.204). Pagina mcp-servers aggiornata.

## 2026-11-13T17:02:33Z

concept_patch: dev-environment/mcp-servers

## 2026-11-13T16:55:43Z

Censimento plugin OpenCode in mcp-servers: @lmnr-ai/opencode-plugin (Laminar tracing) e context-mode documentati con dove e cosa fanno; chiarito che i plugin OpenCode non sono artefatti Cartographer.

## 2026-11-13T16:55:33Z

concept_patch: dev-environment/mcp-servers

## 2026-11-13T16:37:32Z

Skill laya migrata da copia manuale ad artefatto della KB (skills/laya/SKILL.md), materializzata in .opencode/skills/laya del workspace $HOME con provenance. Pagina laya-opencode aggiornata.

## 2026-11-13T16:37:24Z

Skill laya migrata da copia manuale ad artefatto della KB (skills/laya/SKILL.md), materializzata in .opencode/skills/laya del workspace $HOME con provenance. Pagina laya-opencode aggiornata.

## 2026-11-13T16:37:16Z

concept_patch: dev-environment/laya-opencode

## 2026-11-13T16:13:39Z

Server MCP laya migrato da configurazione manuale ad artefatto Cartographer: creato mcp/laya.json, approvato, rimosso l'entry manuale globale, sync materializza nel config di workspace $HOME/opencode.json. Pagina mcp-servers aggiornata.

## 2026-11-13T16:13:34Z

concept_patch: dev-environment/mcp-servers

## 2026-11-13T14:54:55Z

Censimento MCP in dev-environment: creata la pagina mcp-servers (9 server tra globale e progetto family-bot) e due quesiti aperti (config duplicata, credenziali in chiaro). Gate superato.

## 2026-11-13T14:54:55Z

concept_patch: sources/2026-11-13-censimento-server-mcp-opencode-globale-progetto-family-bot-2

## 2026-11-13T14:54:46Z

concept_batch (3 operation(s)):
- dev-environment/mcp-servers (+ index entry)
- dev-environment/mcp-config-duplicata (+ index entry)
- dev-environment/mcp-segreti-in-chiaro (+ index entry)

## 2026-11-13T14:54:11Z

source_register: sources/2026-11-13-censimento-server-mcp-opencode-globale-progetto-family-bot-2

## 2026-11-13T14:50:21Z

Ingest della sessione agente del 2026-11-13 sull'integrazione Laya+OpenCode: registrata la fonte (thread) e create in dev-environment le pagine laya-opencode e docker-run-mcp-env, piu due quesiti aperti (LAYA_PRELOAD non persistito, valore dell'integrazione non provato). Gate superato.

## 2026-11-13T14:50:11Z

concept_patch: sources/2026-11-13-sessione-agente-2026-11-13-integrazione-server-laya-locale-c

## 2026-11-13T14:49:44Z

concept_batch (4 operation(s)):
- dev-environment/laya-opencode (+ index entry)
- dev-environment/docker-run-mcp-env (+ index entry)
- dev-environment/laya-preload-non-persistito (+ index entry)
- dev-environment/valore-laya-non-provato (+ index entry)

## 2026-11-13T14:46:35Z

source_register: sources/2026-11-13-sessione-agente-2026-11-13-integrazione-server-laya-locale-c

## 2026-11-13T14:46:03Z

map_update: dev-environment

## 2026-11-13T14:46:03Z

map_create: sources

## 2026-11-13T14:45:57Z

map_create: dev-environment

