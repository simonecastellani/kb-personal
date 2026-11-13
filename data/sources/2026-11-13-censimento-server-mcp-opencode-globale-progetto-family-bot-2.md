---
type: Source
title: "Censimento server MCP OpenCode (globale + progetto family-bot), 2026-11-13"
source_kind: document
timestamp: "2026-11-13T16:00:00+01:00"
ingest_status: ingested
shape: source
ingested_at: "2026-11-13T16:05:00+01:00"
---
Censimento eseguito il 2026-11-13 leggendo le configurazioni OpenCode sul client.

## Dove sono configurati

- Globale: `$HOME/.config/opencode/opencode.json` e `$HOME/.config/opencode/opencode.jsonc` (entrambi attivi: i server di entrambi risultano connessi).
- Progetto family-bot: `$HOME/project/family-bot/opencode.json`.
- Plugin globali: `$HOME/.config/opencode/plugins/` (cartographer bootstrap/write-findings); `context-mode` dichiarato nel jsonc; `@lmnr-ai/opencode-plugin` nel config di progetto.

## Server (globale)

- **tokensave** — local, `tokensave serve` (brew): code graph MCP. Attivo.
- **cartographer** — local, `cartographer mcp --provider opencode` (brew): server KB. Attivo. Presente anche una entry legacy `mcp.cartographer` fuori da `mcp.servers` (residuo V1).
- **laya** — local, `docker run -i --rm --network laya_default -e LAYA_BASE_URL=http://laya-serve:8000 laya-mcp:latest`: motore decisionale (vedi pagina integrazione). Attivo.
- **tinyfish** — remote, `https://agent.tinyfish.ai/mcp?...`: web search/fetch/automation. Attivo. Usa header `X-API-Key` con valore in chiaro nel jsonc.

## Server (progetto family-bot)

- **tokensave** — local: duplicato del globale.
- **deepwiki** — remote, `https://mcp.deepwiki.com/mcp`. Attivo.
- **laminar** — remote, `http://192.168.178.204:8000/v1/mcp` con header `Authorization: Bearer …` (valore in chiaro nel config di progetto).
- **sonarqube** — local, `docker run --rm -i -e SONARQUBE_TOKEN -e SONARQUBE_URL mcp/sonarqube`: disabilitato; richiede SONARQUBE_URL o SONARQUBE_ORG (fallisce all'avvio senza).
- **codegraph** — local, `codegraph serve --mcp`: disabilitato, codemode false.

## Segreti trovati (non riportati)

Due credenziali in chiaro nelle config: la X-API-Key TinyFish (jsonc globale) e il bearer token Laminar (config di progetto). Da spostare in variabili d'ambiente o SOPS.

## Quesiti aperti

- Config MCP duplicata: tokensave in globale e progetto, `opencode.json` + `opencode.jsonc` entrambi attivi, entry legacy `mcp.cartographer`. Da consolidare?
- Due segreti in chiaro: spostarli in env/SOPS (secret_set).
