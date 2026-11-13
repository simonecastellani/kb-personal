---
type: Source
title: "Sessione agente 2026-11-13: integrazione server Laya locale con OpenCode"
source_kind: thread
timestamp: "2026-11-13T15:45:00+01:00"
ingest_status: pending
---
Distillazione della sessione OpenCode del 2026-11-13 (ore ~14:00–15:45). Puntatori = fasi della conversazione.

## Cosa è emerso

1. **Laya** (ricerca iniziale): modello decisionale open-source 421M parametri (Convai Innovations, Apache-2.0) per routing, scoring e classificazione a domande tipizzate (choice/score/noul) — NON un modello di chat. API Jev-compatibile `POST /v1/systemone`. Installazione esistente: clone del repo in `$HOME/tools/laya` con immagine Docker `laya-laya-serve` e volume `laya_model-cache`.

2. **Architettura realizzata** (fase di installazione):
   - `laya-serve`: container da `compose.yaml` + `compose.http.yaml` in `$HOME/tools/laya`, bind `127.0.0.1:8000`, `restart: unless-stopped`, avviato con `LAYA_PRELOAD=1` (3 checkpoint residenti: english, multilingual, typed-decisions; CPU).
   - `laya-mcp:latest`: immagine derivata costruita da `$HOME/.local/share/laya-mcp/Dockerfile` (base `laya-laya-serve` + `pip install laya[mcp]`, ~1 MB; serve `USER root` per pip e ripristino `USER 10001`).
   - OpenCode: server MCP `laya` globale in `$HOME/.config/opencode/opencode.json` → `docker run -i --rm --network laya_default -e LAYA_BASE_URL=http://laya-serve:8000 laya-mcp:latest` → 10 tool (laya_predict, laya_decide, laya_predict_batch, laya_route, laya_route_batch, laya_preset, laya_shortlist, laya_status, laya_predict_long, laya_tournament).
   - Skill `$HOME/.config/opencode/skills/laya/SKILL.md`: quando usare i tool (classificazione, triage, routing, scoring, sì/no) e limiti.
   - Durante la sessione: binding cartographer `opencode` per la home dell'utente → `kb-personal`.

3. **Causa principale del timeout MCP** (fase di diagnosi, il punto chiave):
   - Sintomo: `opencode mcp list` → "Request timed out" a ~30s; i container laya-mcp venivano creati e restavano vivi con stdin a pos:0.
   - Causa: il campo `environment` della config MCP di OpenCode passa `LAYA_BASE_URL` solo al processo docker CLI; `docker run` NON inoltra il proprio ambiente al container. Senza la variabile, il `laya-mcp-server` partiva in modalità LOCALE scaricando i checkpoint da Hugging Face dentro il container effimero (log stderr di download) e non rispondeva all'initialize prima del timeout.
   - Fix: `-e LAYA_BASE_URL=http://laya-serve:8000` negli argomenti del comando docker (confermato: `docker inspect` del container bloccato mostrava la variabile assente).
   - Nota operativa salvata anche nella skill laya.

4. **Verifica end-to-end** (fase finale): classificazione "CI rossa dopo merge" → `kind: bug` (98%), `urgent: 0.75` in una forward pass, ~1s. `opencode mcp list`: connesso.

5. **Limiti noti in modalità remote/shared-server**: stati più lunghi di una finestra leggono solo la prima finestra via HTTP (`laya_predict_long` e `laya_tournament` sono in-process only e non disponibili); la confidence va trattata come segnale, non verità (zero-shot iperconfidente).

## Domande aperte

- `LAYA_PRELOAD=1` è stato passato ad hoc al primo `up`: non è in `.env`, quindi una ricreazione del container ripaga il cold-start (prima richiesta lenta).
- L'effettivo valore dell'integrazione sul campo non è ancora provato: nessun flusso di lavoro reale la usa ancora.
