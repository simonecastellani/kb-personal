---
status: stable
title: Laya provisionata dal repo homelab sulla VM opencode
type: Reference
---
# Laya provisionata dal repo homelab sulla VM opencode

L'installazione manuale di Laya sulla VM `opencode` (LXC 203, 192.168.178.203) è stata sostituita da un role Ansible idempotente nel repo `homelab`, che espone l'MCP pubblicamente via reverse proxy. Verificato il 2026-11-19.

## Architettura

- **laya-serve** — API HTTP (`laya.serve`) costruita dal sorgente upstream in `/opt/laya/src` (clone pinnato a `laya_git_ref`, oggi `v0.4.31`), pubblicata solo su `127.0.0.1:8001`. `LAYA_PRELOAD=1`, checkpoint english/multilingual/typed-decisions su CPU, volume `laya_model-cache`.
- **laya-mcp** — thin layer `FROM laya-laya-serve` + `pip install laya[mcp]`, in modalità remote (`LAYA_BASE_URL=http://laya-serve:8000`), pubblicata su `0.0.0.0:8000` e servita da Caddy come `https://laya.simonecastellani.com/mcp`.
- **Project compose** — `/opt/laya/compose.yml`, `name: laya`, non usa direttamente i file compose upstream: solo il source tree come build context e il nome immagine, per riusare cache e volume esistenti. Volume pinnato `laya_model-cache`.
- **File del role** — `ansible/roles/laya/`: `compose.yml.j2`, `mcp/Dockerfile`, `mcp_http.py`, `opencode.json.j2` (MCP remoto su `http://127.0.0.1:8000/mcp` per l'utente `opencode`).

## MCP su HTTP

`laya-mcp-server` upstream parla solo stdio. L'oggetto `server` di `laya.mcp.server` è però transport-agnostico: `mcp_http.py` chiama `server.run(transport="streamable-http", host, port)` (mcp>=2.2, già nel thin layer) e il container espone `POST /mcp`. Nessun bridge esterno (mcp-proxy/supergateway) necessario. Testato: initialize 200 e `laya_status` risponde `mode: remote`.

## Autenticazione

Caddy richiede `Authorization: Bearer <LAYA_MCP_TOKEN>` sul blocco `laya.simonecastellani.com` e risponde 401 senza header. Il secret viaggia come `LAYA_MCP_TOKEN` (env nel workflow `apply.yml`, letto con `lookup('env', ...)` dal role `reverse_proxy`). L'hostname va tenuto fuori da Cloudflare Access: i client MCP usano l'header, non una sessione browser.

## Insidie incontrate

- **Porta non pubblicata**: il compose upstream pubblica `${LAYA_BIND_ADDRESS:-127.0.0.1}:${LAYA_PORT:-8000}`; mettere `LAYA_BIND_ADDRESS` sotto `environment:` in un override non cambia il `ports:` (l'interpolazione avviene al parse, non nell'ambiente del container). Per questo la regola Caddy esistente rispondeva `000`. Soluzione: dichiarare `ports:` esplicito.
- **Merge di compose**: le liste `ports` si concatenano tra file -f, quindi un override non può "sostituire" una porta: da qui il compose autonomo.
- **Project name**: con `-f /opt/laya/src/compose.yaml` il project dir sarebbe `src` e il volume sarebbe `src_model-cache` (riscaricherebbe ~2 GB); `name: laya` evita il problema.
- **Gruppo Ansible sbagliato**: `config.yml` dava `role: vps` per opencode mentre il playbook usava `hosts: opencode`; corretto in `role: opencode`. Senza il fix il role laya non sarebbe mai stato eseguito.
- **Ricostruzione**: il marker `/opt/laya/.built-ref` e la presenza dell'immagine condizionano `docker compose build`; la seconda esecuzione del playbook è `changed=0`.

## Verifica

- `ansible-playbook --limit opencode`: changed=0 alla seconda run.
- `curl -X POST https://laya.simonecastellani.com/mcp` con header → 200 e `serverInfo.name=laya`; senza header → 401; `POST /v1/systemone` su `127.0.0.1:8001` non raggiungibile dalla 205.

Vedere [[dev-environment/laya-opencode]] per l'installazione equivalente sul PC locale (stdio, `docker run -i`).
