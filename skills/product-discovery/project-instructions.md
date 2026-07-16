<!-- product-discovery:start -->
# Product Discovery Layer (fase 0 di Superpowers)

## Quando scatta
Prima di invocare `superpowers:brainstorming` (o qualsiasi design):
- **nuovo prodotto**: se non esiste `docs/discovery-brief.md` con `meta.phase: approved`,
  invoca prima `product-discovery` (modalità piena);
- **nuova feature su prodotto con brief approvato**: invoca `product-discovery` in
  **modalità feature** — mini-discovery scopata alla feature: check sul decision log
  dai file, conflitti nominati subito, decisioni via `decision-gate`, delta-brief in
  `docs/features/`. Nessun design con gate aperti, nemmeno condizionale.

## Source of truth
`specs/discovery-state.yaml` (creato dal template in `specs/discovery-state.template.yaml`).
Ogni skill aggiorna solo le sezioni di cui è autrice. Nessuna skill sceglie la skill successiva:
si avanza solo quando il gate d'uscita della skill corrente è soddisfatto.

## Regola ferrea (vale per tutte le skill)
**Nessun artefatto di design, PRD, pitch o piano viene prodotto finché esistono
`pending_decisions` con `impact: high` e `resolved: false`.**
Criticare un'assunzione e poi costruirci sopra comunque equivale ad approvarla.

## Mappa degli artefatti (per riprendere in una nuova sessione)
Percorsi fissi per convenzione — niente indice da mantenere:
- `specs/discovery-state.yaml` — stato corrente; `meta.phase` dice a che punto sei
- `docs/interviews/session-XX.md` — trascrizioni interviste (scrive: product-discovery)
- `docs/decisions/decision-log.md` — decisioni prese, append-only (scrive: decision-gate)
- `docs/discovery-brief.md` — output finale della discovery (scrive: pre-brainstorm-brief)
- `docs/features/NNN-<slug>-brief.md` — delta-brief per feature (scrive: product-discovery, modalità feature)
- `spikes/` — codice usa-e-getta degli spike; i verdetti stanno in `evidence` dello stato

Per riprendere: leggi `meta.phase` e le `pending_decisions` con `resolved: false` dallo stato.
Lo stato è la verità — non ricostruirla dalla cronologia della chat.

## Handoff contract verso brainstorming
Quando `superpowers:brainstorming` parte dopo la discovery, riceve `docs/discovery-brief.md` e:
- tratta i punti `confirmed` come vincoli non rinegoziabili;
- tratta le `pending_decisions` residue come domande da porre all'utente — MAI da risolvere da solo;
- non riapre decisioni presenti in `docs/decisions/decision-log.md` senza chiederlo esplicitamente;
- eredita le `assumptions` non validate come rischi dichiarati nel design, non come fatti;
- parte preferibilmente in una **sessione nuova**: tutto il necessario è su file, la cronologia
  della discovery non serve e non va usata (contesto lungo = più sycophancy, meno aderenza).
<!-- product-discovery:end -->
