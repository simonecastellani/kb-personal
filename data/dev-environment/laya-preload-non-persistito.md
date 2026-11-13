---
contradiction_kind: open_question
involves: [dev-environment/laya-opencode]
status: open
title: LAYA_PRELOAD=1 non persistito in .env del compose
type: Contradiction
---
# LAYA_PRELOAD=1 non persistito in .env del compose

Il primo avvio di `laya-serve` ha ricevuto `LAYA_PRELOAD=1` solo dalla riga di comando del `docker compose up`: non è in un file `.env` del progetto. Una ricreazione del container (es. dopo `docker compose build` o un cambio di configurazione) ripaga il cold-start: la prima richiesta attende il caricamento dei checkpoint.

Persistirlo (`.env` con `LAYA_PRELOAD=1` nel repo `$HOME/tools/laya`) e verificare che il container lo recepisca alla prossima ricreazione.
