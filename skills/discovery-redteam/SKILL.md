---
name: discovery-redteam
description: Use after product discovery interviews, when discovery-state.yaml has assumptions or the user asks to pressure-test, stress-test, or find blind spots in a product idea before designing it.
---

# Discovery Red-Team

## Overview

Pressure-test del quadro emerso dalla discovery. **Chi ha raccolto non può giudicare**: la stessa sessione che ha costruito il quadro con l'utente è la meno adatta a demolirlo (momentum conversazionale). Il giudizio arriva da contesti freschi e da evidenze esterne — non da te.

## Passi

**1. Dispatch di 3-4 subagent in parallelo.** Ognuno riceve SOLO il contenuto di `specs/discovery-state.yaml` (niente storia della chat, niente entusiasmo da proteggere) e una lente.
Se il runtime non ha subagent nativi (Agent/Task tool), ottieni lo stesso contesto fresco lanciando invocazioni one-shot della CLI via shell — es. `codex exec "<prompt>"`, `opencode run "<prompt>"` — passando nel prompt solo il contenuto dello stato e la lente. Se nemmeno questo è possibile, chiedi all'utente di girare i tre prompt in sessioni nuove e incollare i risultati: la freschezza del contesto è il punto, non rinunciarci.
Le lenti:

- **Utente scettico**: "Sei il target descritto ma non compreresti mai. Perché? Cosa risolve già il tuo problema oggi a costo zero?"
- **Mercato/competitor**: "Elenca chi fa già questo o lo rende irrilevante. La claim di unicità regge?"
- **Economics**: "Il modello di monetizzazione regge? Chi paga, quanto, perché, e cosa dice la storia di prodotti simili?"
- **Miglior pratica altrui** (lente costruttiva, non demolitrice): "Chi ha già risolto questo problema, e cosa fa *bene*? Competitor diretti se esistono; altrimenti **analoghi anche di altre categorie** — il problema 'export dati' o 'onboarding' è risolto bene da prodotti lontanissimi dal tuo, e per una feature interna gli analoghi sono altri software con lo stesso problema UX. Feature chiave, onboarding, pricing, posizionamento; cosa amano gli utenti nelle recensioni; cosa è standard che ignorare costerebbe caro." Copiare ciò che funziona e perfezionarlo è una strategia, non una vergogna — ma va scelto, non subìto. **Se non esiste nulla di rilevante: dirlo, senza inventare analoghi per riempire la sezione. E l'assenza è essa stessa evidenza da interpretare — nessuno l'ha fatto perché il mercato non c'è, o è un'opportunità vera? Domanda da girare alle lenti critiche, non casella vuota.**

Le tre lenti critiche restituiscono blind spot con severità e domanda risolutiva; istruiscile: "Il tuo compito è confutare, non bilanciare. Se non trovi problemi, dillo, ma non inventare pregi." La lente miglior-pratica restituisce pattern con evidenza e fonte; istruiscila: "Riporta solo ciò che ha segnali reali di funzionamento (recensioni, adozione, longevità), non ciò che ti sembra elegante."

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

**4b. Esposizione del "cosa funziona già".** I pattern trovati dalla lente miglior-pratica non restano sepolti nello stato: presentali all'utente in una sezione dedicata — per ciascuno: cosa fa il competitor, perché funziona (evidenza/recensioni, con fonte), e la scelta che apre: **adottare / adattare / differenziarsi deliberatamente**. Ogni pattern che tocca scope, pricing o posizionamento diventa una `pending_decision` (impact almeno `med`) e passa dal gate. Guardrail: "lo fanno tutti" non è evidenza che funzioni *per il tuo target* — il pattern si adotta con l'evidenza, non per imitazione cieca; adottare un pattern non bypassa mai una decisione già a log; e **omettere un pattern perché confligge con una decisione presa = decidere al posto dell'utente**: si espone col conflitto nominato, la scelta resta sua.

**5. Loop di approfondimento (il red-team non è un passaggio unico).** Ogni blind spot con una `resolving_question` a cui può rispondere solo l'utente torna all'intervista: poni le domande mirate (stile `product-discovery`: una alla volta, esempi concreti), registra le risposte come nuove `confirmed`/`assumptions`. Se le risposte cambiano il quadro in modo sostanziale, ri-esegui il red-team sul quadro aggiornato — le lenti fresche non hanno visto le risposte nuove. Massimo 2 giri completi: i blind spot ancora aperti al termine diventano `pending_decisions` o rischi dichiarati, non si trascinano. Chiudere il red-team con le domande risolutive mai poste all'utente = consegnare al gate un quadro monco.

## Cosa NON fai

- Non risolvi i blind spot: li trasformi in domande o decisioni.
- Non ammorbidisci i verdetti dei subagent per riguardo all'utente: riporta il dissenso com'è, con le fonti.
- Non proponi il design "corretto": non è ancora il momento del design.

## Gate d'uscita

Passa a `decision-gate` (che si apre con la **decisione zero**: GO / PIVOT / STOP) quando ogni blind spot `severity: high` è `resolved` (chiarito con l'utente tramite il loop del passo 5) o convertito in `pending_decision`, e le `resolving_question` rivolte all'utente hanno avuto risposta o sono diventate decisioni. Se il red-team rivela che la discovery è troppo debole (target inesistente, problema non osservato), torna a fare domande — dicendo esattamente quali.
