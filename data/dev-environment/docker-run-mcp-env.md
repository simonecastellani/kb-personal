---
provenance: [sources/2026-11-13-sessione-agente-2026-11-13-integrazione-server-laya-locale-c]
status: stable
title: "docker run non eredita l'ambiente del processo CLI"
type: Lesson
---
# docker run non eredita l'ambiente del processo CLI

Il campo `environment` di un server MCP locale (es. OpenCode) viene passato al **processo spawnato** — nel caso di `docker run`, al docker CLI. Docker **non inoltra** il proprio ambiente al container: la variabile arriva al CLI e muore lì. Un container MCP stdio che dipende da una variabile d'ambiente per la sua modalità di funzionamento parte quindi nella modalità sbagliata, in silenzio.

## Sintomo (caso reale)

`laya-mcp-server` in container senza `LAYA_BASE_URL`: invece di fare proxy verso il server HTTP, partiva in **modalità locale** scaricando i checkpoint da Hugging Face dentro il container effimero → nessuna risposta all'initialize → il client MCP va in "Request timed out" (~30s) mentre il container resta vivo. Diagnosi distintiva: `docker inspect` del container mostra la variabile assente da `Config.Env`; lo stderr del container mostra il download; lo stdin del processo serve resta a `pos: 0`.

## Fix

Passare la variabile negli argomenti del comando: `-e LAYA_BASE_URL=http://laya-serve:8000` (vedere [[dev-environment/laya-opencode]]).

## Regola generale

Per ogni server MCP eseguito via `docker run`, tutta la configurazione richiesta dal container deve viaggiare come flag del comando (`-e`, `--network`, mount), mai contando sull'ambiente ereditato dal client.
