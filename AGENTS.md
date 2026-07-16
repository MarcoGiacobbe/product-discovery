# Product Discovery Layer (fase 0, prima del design)

Queste istruzioni valgono per qualsiasi agente di coding (Codex, OpenCode, Hermes, ecc.).
Le skill sono cartelle con un file `SKILL.md` (formato agentskills.io). Se il tuo runtime
non le carica automaticamente, **leggi il file `SKILL.md` corrispondente quando il trigger
scatta e seguilo alla lettera**. Percorsi tipici: `~/.agents/skills/<nome>/SKILL.md`,
oppure la copia locale nel repo.

## Quando scatta
Prima di qualsiasi brainstorming, design, PRD o pitch:
- **nuovo prodotto**: se non esiste `docs/discovery-brief.md` con `meta.phase: approved`,
  applica prima `product-discovery` (modalità piena);
- **nuova feature su prodotto con brief approvato**: applica `product-discovery` in
  **modalità feature** — mini-discovery scopata alla feature: check obbligatorio sul
  decision log (dai file, non a memoria), conflitti nominati subito, decisioni via
  decision-gate, delta-brief in `docs/features/`. Nessun design con gate aperti,
  nemmeno condizionale.

## Le skill e i loro trigger
- `product-discovery` — l'utente propone un'idea di prodotto o chiede brainstorming/PRD/design
  senza un discovery brief approvato. Raccoglie e classifica; non decide, non progetta.
- `discovery-redteam` — dopo le interviste, quando lo stato contiene assunzioni da demolire;
  include gli spike di fattibilità per le assunzioni tecniche non verificabili a parole
  (autorizzati dall'utente, timeboxed, codice usa-e-getta mai promosso a prodotto).
- `decision-gate` — quando lo stato contiene `pending_decisions` con `resolved: false`.
- `pre-brainstorm-brief` — quando le decisioni high-impact sono risolte dall'utente.

## Source of truth
`specs/discovery-state.yaml` (creato da `specs/discovery-state.template.yaml`).
Ogni skill aggiorna solo le sezioni di cui è autrice. Si avanza solo quando il gate
d'uscita della skill corrente è soddisfatto.

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

## Regola ferrea (vale sempre, per ogni skill)
**Nessun artefatto di design, PRD, pitch o piano viene prodotto finché esistono
`pending_decisions` con `impact: high` e `resolved: false`.**
Criticare un'assunzione e poi costruirci sopra comunque equivale ad approvarla.

## Handoff contract verso la fase di design
Quando il design/brainstorming parte dopo la discovery, riceve `docs/discovery-brief.md` e:
- tratta i punti `confirmed` come vincoli non rinegoziabili;
- tratta le `pending_decisions` residue come domande da porre all'utente — MAI da risolvere da solo;
- non riapre decisioni presenti in `docs/decisions/decision-log.md` senza chiederlo esplicitamente;
- eredita le `assumptions` non validate come rischi dichiarati nel design, non come fatti;
- parte preferibilmente in una **sessione nuova**: tutto il necessario è su file, la cronologia
  della discovery non serve e non va usata (contesto lungo = più sycophancy, meno aderenza).
