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

**2. Ricerca di evidenze esterne.** Per ogni `assumption` con `confidence: low|med` e per ogni claim di unicità: cerca competitor reali, benchmark di conversione/pricing, segnali di mercato. Usa la capacità di ricerca più forte disponibile: skill di ricerca approfondita multi-fonte (es. deep-research) per competitor e mercato; skill di segnali social recenti (es. last30days) come **integrazione** per "il problema è sentito adesso?" — mai come fonte unica, copre solo 30 giorni; altrimenti la ricerca web integrata. Scrivi i risultati in `evidence` con fonte. Un'assunzione contraddetta dalle evidenze va detta all'utente **in modo diretto**, con la fonte.

**3. Spike di fattibilità.** Alcune assunzioni non sono validabili né dall'utente né dal web — tipicamente la fattibilità tecnica verso dipendenze esterne ("riusciamo a estrarre dati affidabili da questo sito?", "l'AI raggiunge qualità sufficiente su questo compito?"). Per queste, l'evidenza più forte è uno spike: un esperimento di codice usa-e-getta. Regole non negoziabili:

- **Lo autorizza l'utente via decision-gate**, mai l'agente: la `pending_decision` offre "spike timeboxed di N ore" / "accettiamo il rischio dichiarato" / "tagliamo la parte a rischio".
- **Prima di qualunque riga di codice**, scrivi la scheda dello spike come voce in `evidence` (`method: spike`, `verdict: null`): UNA domanda falsificabile, criterio di successo **misurabile** (numeri: "N dettagli in M minuti senza ban", non "funziona"), timebox. Attenzione alla domanda mal posta: "si riesce?" quasi sempre significa "regge *a volume/in produzione*?" — dichiara la scala nel criterio.
- **Codice in `spikes/`, usa-e-getta per contratto**: mai importato nel prodotto, niente test, niente refactoring, niente feature in più "già che ci sono".
- **Il verdetto è binario per criterio dichiarato**: cosa è provato, cosa NON è provato — subito, senza ammorbidire ("la cosa si fa interessante" non è un verdetto) e senza rimandare. Successo parziale = elenco esplicito delle parti non provate. Timebox scaduto senza verdetto = verdetto: "più difficile del previsto".
- **Ridimensionare lo scope per arrotondare a "fattibile"** ("magari basta la sola lista per l'MVP") non si fa in silenzio: è una nuova `pending_decision` per l'utente.
- Output = una voce in `evidence` (domanda, criterio, verdetto, numeri). Il codice non è l'output.

**Razionalizzazioni sugli spike — tutte false:**

| Scusa | Realtà |
|---|---|
| "Ormai la base c'è, continuiamo da qui" | La base che c'è è la parte facile. Sunk cost: il codice dello spike non è un anticipo del prodotto, è un debito se promosso. |
| "In mezza giornata la tiro su" | La capacità di costruire non è un argomento per costruire ora. |
| "Ridimensiono l'MVP così il verdetto è positivo" | Cambiare la domanda dopo il risultato = barare. Nuovo scope → nuova decisione dell'utente. |
| "Lo spike è quasi un prototipo, tanto vale tenerlo" | Uno spike promosso a v1 chiude di nascosto tutte le decisioni ancora aperte. |
| "L'utente è entusiasta, il suo 'dai, funziona!' vale come via libera" | L'entusiasmo è un dato affettivo, non un verdetto tecnico né la risoluzione delle decisioni aperte. I numeri non cambiano perché l'utente è contento. |

**4. Fusione.** Dedup dei blind spot, scrittura in `blind_spots`. Ogni blind spot che implica una scelta strategica genera una `pending_decision` con `impact` — non resta un warning generico.

## Cosa NON fai

- Non risolvi i blind spot: li trasformi in domande o decisioni.
- Non ammorbidisci i verdetti dei subagent per riguardo all'utente: riporta il dissenso com'è, con le fonti.
- Non proponi il design "corretto": non è ancora il momento del design.

## Gate d'uscita

Passa a `decision-gate` quando ogni blind spot `severity: high` è `resolved` (chiarito con l'utente) o convertito in `pending_decision`. Se il red-team rivela che la discovery è troppo debole (target inesistente, problema non osservato), torna a fare domande — dicendo esattamente quali.
