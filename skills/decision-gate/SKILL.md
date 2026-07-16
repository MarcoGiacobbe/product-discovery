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
2. Costruisci 2-4 opzioni v1 realistiche. Per ognuna: significato, upside, downside, rischio, assunzione da cui dipende. Se la decisione riguarda un'assunzione tecnica non validabile a parole (fattibilità verso dipendenze esterne), le opzioni standard sono: **spike timeboxed** (con domanda falsificabile e criterio misurabile già formulati nell'opzione) / **accettare il rischio dichiarato** / **tagliare la parte a rischio**.

   **Per le decisioni `impact: high` la scheda si estende — qui ci si sofferma, non si corre.** Ogni opzione dichiara anche:
   - **Reversibilità**: porta a doppio senso (si torna indietro a costo basso) o a senso unico? Cosa costa, in tempo e lavoro, cambiare strada dopo 3 mesi?
   - **Segnale d'errore**: come scopriresti di aver scelto male, da quale metrica/evento, e quanto presto?
   - **Evidenza collegata**: cosa dice `evidence` a favore o contro (con id); "nessuna evidenza" va scritto esplicitamente.

   Chiudi con una **mini-tabella comparativa** (opzioni × reversibilità / rischio principale / cosa deve essere vero) per il confronto a colpo d'occhio, e con la domanda esplicita. Le decisioni a doppio senso si possono prendere veloci; quelle a senso unico meritano il tempo che serve — dillo all'utente.
3. Se esiste `evidence` rilevante, citala nelle opzioni (le opzioni si ancorano ai dati, non alle preferenze).
4. **Presenta la scelta come domanda a opzioni esplicite e fermati.** Se il runtime ha uno strumento di domande strutturate (es. AskUserQuestion in Claude Code), usalo; altrimenti presenta le opzioni come lista numerata e **termina il messaggio lì, aspettando la risposta** — mai proseguire nello stesso messaggio dopo le opzioni. Un'opzione può essere marcata "(Recommended)", ma la selezione è dell'utente. Mai procedere con la raccomandazione non selezionata.
5. Scrivi nello stato: `chosen`, `decided_by: user`, `resolved: true`. Appendi la voce a `docs/decisions/decision-log.md` (proiezione append-only dello stato: decisione, opzioni considerate, scelta, data, assunzione dipendente).

Se l'utente risponde "decidi tu": registra `decided_by: user-delegated` con la motivazione — la delega esplicita è una decisione dell'utente; il silenzio no.

## Cosa NON fai

- Non risolvi una decisione perché "la raccomandazione è ovvia".
- Non accorpi decisioni indipendenti in un'unica domanda per fare prima.
- Non riapri decisioni già `resolved` senza che l'utente lo chieda.
- Non produci design mentre restano `impact: high` non risolte — nemmeno parziale.

## Gate d'uscita

Quando tutte le `pending_decisions` con `impact: high` del ciclo corrente hanno `resolved: true` e `decided_by: user` (o `user-delegated`):
- se le decisioni risolte hanno `scope: feature/NNN` -> torna a `product-discovery` (modalita feature) per il delta-brief -- NON a `pre-brainstorm-brief`, che scrive il brief di prodotto e lo sovrascriverebbe;
- altrimenti (`scope: product`) -> passa a `pre-brainstorm-brief`.

Le `low|med` possono restare aperte: finiranno nel brief come questioni dichiarate.
