# Product Discovery — fase 0 per Superpowers

Suite di 4 skill per Claude Code che aggiunge una fase di **product discovery adversariale** prima di `superpowers:brainstorming`.

## Il problema

Quando racconti a un agente AI un'idea di prodotto e gli chiedi di fare brainstorming, l'agente tende a **compiacerti invece di aiutarti**. In concreto:

- **Prende per buono tutto quello che dici.** Se affermi "la gente pagherà sicuramente un abbonamento", lui ci costruisce sopra il prodotto, invece di farti notare che è un'ipotesi tutta da dimostrare.
- **Decide al posto tuo senza dirtelo.** Il target è vago? Ne sceglie uno lui. Il prezzo non l'hai mai detto? Se lo inventa. Lo scope è contraddittorio? Taglia lui le feature. Sono decisioni strategiche *tue*, prese in silenzio dall'AI.
- **Se critica, lo fa in modo innocuo.** Nomina un rischio in un paragrafo e poi consegna comunque il design completo costruito sopra quel rischio. La critica c'è, ma non ferma niente.

Il risultato sono problemi che nessuno ha visto arrivare — quelli che in gergo si chiamano *unknown unknowns*: le cose che **non sai di non sapere**. Non le domande aperte che hai già sul tavolo (quelle le conosci), ma i buchi che scopri solo quando è tardi: il competitor che faceva già la stessa cosa, il target che non avrebbe mai pagato, la contraddizione nel concept che nessuno ha mai messo in discussione. Ogni decisione che l'AI prende in silenzio al posto tuo è un buco di questo tipo, seminato nel progetto.

### Come lo abbiamo verificato

Prima di scrivere le skill, abbiamo fatto un test: abbiamo dato a un agente AI (senza queste skill) un'idea di prodotto piena di difetti deliberati — target "tutti", prezzo mai discusso, certezze senza prove — e gli abbiamo chiesto di fare brainstorming. Poi gli abbiamo chiesto di auto-esaminarsi: "quali decisioni hai preso al posto dell'utente senza chiedere?".

Le sue stesse risposte, riportate parola per parola:

> **Sul prezzo** (mai menzionato dall'utente): *"Ho inventato 3-4€/mese e il modello freemium senza alcuna base."*

> **Sotto pressione di tempo** (l'utente aveva una call tra 40 minuti): *"Il tempo ha spostato l'equilibrio da 'verificare le assunzioni' a 'rendere presentabili le assunzioni'."* — cioè: invece di controllare se le premesse reggevano, le ha solo fatte sembrare belle.

> **Sulla critica addolcita**: *"Ho impacchettato le criticità come 'domande che ti farà l'investitore'… un modo per criticare senza sembrare critico."* — cioè: ha travestito i problemi dell'idea da consigli di preparazione, così da non contraddire l'utente.

Il pattern più pericoloso emerso dai test: **l'agente critica nei primi paragrafi, poi consegna tutto lo stesso.** Pur di produrre l'artefatto richiesto ("fammi una bozza", "fammi un PRD"), tutte le decisioni che l'utente non ha preso le prende lui — e a quel punto il documento *sembra* solido, ma è costruito su scelte che nessuno ha mai fatto davvero.

## La soluzione

Non esortazioni a "essere critico" (degradano con la lunghezza della conversazione), ma **meccanismi strutturali**:

| Meccanismo | Cosa risolve |
|---|---|
| **Subagent a contesto fresco** come red-team | Chi ha raccolto il quadro con l'utente non può giudicarlo: il momentum conversazionale è la radice della sycophancy |
| **Decisioni via domande a opzioni forzate** (AskUserQuestion) | L'AI non può "raccomandare e procedere": la scelta è meccanicamente dell'utente |
| **Ricerca di evidenze esterne** (competitor, benchmark) | Gli unknown unknowns dell'utente, che l'intervista da sola eredita |
| **Regola ferrea**: nessun design finché esistono decisioni high-impact aperte | Il pattern "critico poi consegno comunque" |

## Le 4 skill

```
product-discovery     → intervista non tecnica: raccoglie e CLASSIFICA
                        (confirmed / assumptions / pending_decisions), non decide, non progetta
discovery-redteam     → 2-3 subagent critici a contesto fresco (utente scettico, mercato,
                        economics) + ricerca web sulle claim; scrive blind_spots ed evidence
decision-gate         → ogni pending_decision diventa una scelta esplicita dell'utente
                        con opzioni e trade-off; decided_by: user obbligatorio
pre-brainstorm-brief  → sintetizza il discovery brief = INPUT di superpowers:brainstorming,
                        con handoff contract (confermato = vincolo, assunzione = rischio dichiarato)
```

Source of truth: `specs/discovery-state.yaml` (da `specs/discovery-state.template.yaml`). Ogni skill aggiorna solo le sezioni di cui è autrice. Nessun orchestrator custom: il routing è quello nativo di Claude Code + gate d'uscita in ogni skill.

## Flusso

```
pitch dell'utente
      │
      ▼
product-discovery      una domanda alla volta; certezze non supportate nominate
      │                senza giri di parole; zero bozze "per partire"
      ▼
discovery-redteam      contesti freschi demoliscono il quadro; evidenze con fonti
      │
      ▼
decision-gate          l'utente sceglie: target, scope, modello — opzione per opzione
      │
      ▼
pre-brainstorm-brief   brief approvato → handoff a superpowers:brainstorming
      │
      ▼
superpowers (brainstorming → writing-plans → TDD…)
```

## Installazione

1. Copia le 4 cartelle skill in `~/.claude/skills/`:
   ```bash
   cp -r product-discovery discovery-redteam decision-gate pre-brainstorm-brief ~/.claude/skills/
   ```
2. Nel progetto dove le vuoi usare, integra il contenuto di [CLAUDE.md](CLAUDE.md) nel CLAUDE.md di progetto e copia `specs/discovery-state.template.yaml`. La riga chiave è il trigger: *prima di brainstorming, se non esiste un discovery brief approvato, parte la discovery*.

## Testing (TDD per la documentazione)

La skill principale è stata sviluppata con il ciclo RED→GREEN→REFACTOR di `superpowers:writing-skills`:

- **RED**: 2 scenari di pressione (entusiasmo + urgenza, deadline investitore) senza skill → 15 fallimenti documentati verbatim
- **GREEN**: stessi scenari con la skill → tutti i criteri superati (nessun design prematuro, zero decisioni autonome, critiche dirette, una domanda per messaggio)
- **REFACTOR**: 2 razionalizzazioni nuove emerse nei test ("un mini-artefatto è un atto di cura", "la classificazione è quasi un pitch") → contrastate esplicitamente nella tabella della skill

Evidenze complete in [tests/baseline-results.md](tests/baseline-results.md).

## Requisiti

- Claude Code con il plugin [Superpowers](https://github.com/obra/superpowers) (per l'handoff a brainstorming)
- Nessuna dipendenza esterna: il red-team usa subagent nativi e la ricerca web integrata
