---
name: pre-brainstorm-brief
description: Use when discovery is complete — high-impact decisions resolved by the user — and the team is ready to hand off to the design/brainstorming phase (superpowers:brainstorming when available).
---

# Pre-Brainstorm Brief

## Overview

Sintetizza la discovery in `docs/discovery-brief.md`: l'**input** di `superpowers:brainstorming`, non un PRD. Il brief trasporta il confine tra ciò che è deciso e ciò che non lo è, così brainstorming non può riaprire il primo né auto-risolvere il secondo.

## Precondizione (verificala, non assumerla)

Tutte le `pending_decisions` con `impact: high` sono `resolved: true` con `decided_by: user|user-delegated`. Se no, fermati e torna al decision gate — un brief scritto sopra decisioni aperte le chiude di nascosto.

## Struttura del brief

1. **Idea di prodotto** (una frase)
2. **Problema** — solo da `confirmed` ed `evidence`
3. **Utente primario** — come deciso, con id della decisione
4. **Alternative attuali** — da `evidence`, con fonti
5. **Ipotesi di valore** — dichiarata come ipotesi
6. **Decisioni prese** — tabella: decisione, scelta, assunzione dipendente (dal decision log)
7. **Assunzioni aperte** — ogni `assumption` non validata, con confidence: sono i rischi che il design dovrà dichiarare
8. **Blind spot residui** — i `low|med` non risolti
9. **Cosa validare per primo** — priorità legate alle assunzioni più rischiose

## Regole di scrittura

- Preserva l'incertezza onestamente: separare confermato / deciso / assunto è il valore del documento.
- Non inventare dettagli assenti dal materiale di discovery.
- Niente feature list, niente stack, niente roadmap: quello è il lavoro di brainstorming.

## Chiusura e handoff

1. Chiedi all'utente l'approvazione esplicita del brief. Se approvato: `meta.phase: approved`.
2. Handoff alla fase di design con il contratto (da CLAUDE.md/AGENTS.md): `confirmed` e decisioni = vincoli non rinegoziabili; assunzioni aperte = rischi da dichiarare nel design; decisioni residue = domande da porre all'utente, mai da risolvere in autonomia. Se Superpowers è installato, invoca `superpowers:brainstorming` passando il brief come contesto; altrimenti avvia il normale flusso di design/planning del runtime usando il brief come unico input.
