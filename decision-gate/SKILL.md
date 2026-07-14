---
name: decision-gate
description: Use when discovery-state.yaml contains pending_decisions with resolved false, or when the user must choose between strategic product options (target, scope, business model, market) before any design work starts.
---

# Decision Gate

## Overview

Trasforma ogni decisione aperta in una scelta esplicita **fatta dall'utente**, mai dall'AI. Questo è il meccanismo strutturale contro le auto-assunzioni: la scelta passa da un'interfaccia a opzioni forzate, non da prosa in cui l'AI può "raccomandare e procedere".

## Comportamento

Per ogni `pending_decision` con `resolved: false`, in ordine di `impact`:

1. Definisci la decisione in una frase e perché incide sull'esito del prodotto.
2. Costruisci 2-4 opzioni v1 realistiche. Per ognuna: significato, upside, downside, rischio, assunzione da cui dipende.
3. Se esiste `evidence` rilevante, citala nelle opzioni (le opzioni si ancorano ai dati, non alle preferenze).
4. **Presenta la scelta con AskUserQuestion** — un'opzione può essere marcata "(Recommended)", ma la selezione è dell'utente. Mai procedere con la raccomandazione non selezionata.
5. Scrivi nello stato: `chosen`, `decided_by: user`, `resolved: true`. Appendi la voce a `docs/decisions/decision-log.md` (proiezione append-only dello stato: decisione, opzioni considerate, scelta, data, assunzione dipendente).

Se l'utente risponde "decidi tu": registra `decided_by: user-delegated` con la motivazione — la delega esplicita è una decisione dell'utente; il silenzio no.

## Cosa NON fai

- Non risolvi una decisione perché "la raccomandazione è ovvia".
- Non accorpi decisioni indipendenti in un'unica domanda per fare prima.
- Non riapri decisioni già `resolved` senza che l'utente lo chieda.
- Non produci design mentre restano `impact: high` non risolte — nemmeno parziale.

## Gate d'uscita

Passa a `pre-brainstorm-brief` quando tutte le `pending_decisions` con `impact: high` hanno `resolved: true` e `decided_by: user` (o `user-delegated`). Le `low|med` possono restare aperte: finiranno nel brief come questioni dichiarate.
