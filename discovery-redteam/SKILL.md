---
name: discovery-redteam
description: Use after product discovery interviews, when discovery-state.yaml has assumptions or the user asks to pressure-test, stress-test, or find blind spots in a product idea before designing it.
---

# Discovery Red-Team

## Overview

Pressure-test del quadro emerso dalla discovery. **Chi ha raccolto non può giudicare**: la stessa sessione che ha costruito il quadro con l'utente è la meno adatta a demolirlo (momentum conversazionale). Il giudizio arriva da contesti freschi e da evidenze esterne — non da te.

## Passi

**1. Dispatch di 2-3 subagent critici in parallelo.** Ognuno riceve SOLO il contenuto di `specs/discovery-state.yaml` (niente storia della chat, niente entusiasmo da proteggere) e una lente.
Se il runtime non ha subagent nativi (Agent/Task tool), ottieni lo stesso contesto fresco lanciando invocazioni one-shot della CLI via shell — es. `codex exec "<prompt>"`, `opencode run "<prompt>"` — passando nel prompt solo il contenuto dello stato e la lente. Se nemmeno questo è possibile, chiedi all'utente di girare i tre prompt in sessioni nuove e incollare i risultati: la freschezza del contesto è il punto, non rinunciarci.
Le tre lenti:

- **Utente scettico**: "Sei il target descritto ma non compreresti mai. Perché? Cosa risolve già il tuo problema oggi a costo zero?"
- **Mercato/competitor**: "Elenca chi fa già questo o lo rende irrilevante. La claim di unicità regge?"
- **Economics**: "Il modello di monetizzazione regge? Chi paga, quanto, perché, e cosa dice la storia di prodotti simili?"

Ogni subagent restituisce blind spot con severità e domanda risolutiva. Istruiscilo: "Il tuo compito è confutare, non bilanciare. Se non trovi problemi, dillo, ma non inventare pregi."

**2. Ricerca di evidenze esterne.** Per ogni `assumption` con `confidence: low|med` e per ogni claim di unicità: cerca sul web competitor reali, benchmark di conversione/pricing, segnali di mercato. Scrivi i risultati in `evidence` con fonte. Un'assunzione contraddetta dalle evidenze va detta all'utente **in modo diretto**, con la fonte.

**3. Fusione.** Dedup dei blind spot, scrittura in `blind_spots`. Ogni blind spot che implica una scelta strategica genera una `pending_decision` con `impact` — non resta un warning generico.

## Cosa NON fai

- Non risolvi i blind spot: li trasformi in domande o decisioni.
- Non ammorbidisci i verdetti dei subagent per riguardo all'utente: riporta il dissenso com'è, con le fonti.
- Non proponi il design "corretto": non è ancora il momento del design.

## Gate d'uscita

Passa a `decision-gate` quando ogni blind spot `severity: high` è `resolved` (chiarito con l'utente) o convertito in `pending_decision`. Se il red-team rivela che la discovery è troppo debole (target inesistente, problema non osservato), torna a fare domande — dicendo esattamente quali.
