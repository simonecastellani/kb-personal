---
provenance: [sources/2026-11-13-sessione-agente-2026-11-13-integrazione-server-laya-locale-c]
status: stable
title: Integrazione server Laya locale con OpenCode
type: Reference
---
# Integrazione server Laya locale con OpenCode

[Laya](https://github.com/NandhaKishorM/laya) è un modello decisionale locale (421M parametri, Apache-2.0): risponde a domande tipizzate (choice/score/noul) su un testo in una forward pass, senza generare testo. L'integrazione lo espone a OpenCode come server MCP, per decisioni a costo quasi nullo (classificazione, triage, routing, scoring, sì/no) senza consumare token del modello principale.

## Architettura

- **laya-serve** — container Docker da `compose.yaml` + `compose.http.yaml` nel repo `$HOME/tools/laya`; ascolta su `127.0.0.1:8000` (endpoint Jev-compatibile `POST /v1/systemone`), `restart: unless-stopped`, checkpoint residenti: english, multilingual, typed-decisions (CPU). Riavvio: `docker compose -f compose.yaml -f compose.http.yaml up -d laya-serve`.
- **laya-mcp:latest** — immagine proxy costruita da `$HOME/.local/share/laya-mcp/Dockerfile`: base `laya-laya-serve` + extra `laya[mcp]` (~1 MB). In modalità remote (con `LAYA_BASE_URL`) risponde in ~1s senza torch in esecuzione. Ricostruire dopo un `docker compose build`: `docker build -t laya-mcp:latest $HOME/.local/share/laya-mcp`.
- **OpenCode** — server MCP `laya` globale in `$HOME/.config/opencode/opencode.json`: `docker run -i --rm --network laya_default -e LAYA_BASE_URL=http://laya-serve:8000 laya-mcp:latest`. Espone 10 tool: laya_predict, laya_predict_batch, laya_decide, laya_preset, laya_shortlist, laya_route, laya_route_batch, laya_status, laya_predict_long, laya_tournament.
- **Skill** `$HOME/.config/opencode/skills/laya/SKILL.md` — insegna all'agente quando usare i tool e come operare il servizio.

Il `-e LAYA_BASE_URL` nel comando docker è obbligatorio: vedere [[dev-environment/docker-run-mcp-env]].

## Limiti in modalità remote

- Stati più lunghi di una finestra di contesto: via HTTP si legge solo la prima finestra; `laya_predict_long` e `laya_tournament` sono in-process only e non disponibili.
- La confidence va trattata come segnale, non verità: zero-shot iperconfidente su domande fuori distribuzione.
- `laya_predict_batch`: fino a 64 stati per chiamata.

## Quesiti aperti

- [[dev-environment/laya-preload-non-persistito]] — LAYA_PRELOAD non persistito in `.env`: il cold-start torna alla ricreazione.
- [[dev-environment/valore-laya-non-provato]] — valore dell'integrazione da provare su flussi reali.

## Verifica

Test end-to-end (2026-11-13): "CI rossa dopo merge" → `kind: bug` (98%), `urgent: 0.75`, ~1s per forward pass. `opencode mcp list` connesso.
