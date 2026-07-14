# Product Discovery Layer (fase 0, prima del design)

Queste istruzioni valgono per qualsiasi agente di coding (Codex, OpenCode, Hermes, ecc.).
Le skill sono cartelle con un file `SKILL.md` (formato agentskills.io). Se il tuo runtime
non le carica automaticamente, **leggi il file `SKILL.md` corrispondente quando il trigger
scatta e seguilo alla lettera**. Percorsi tipici: `~/.agents/skills/<nome>/SKILL.md`,
oppure la copia locale nel repo.

## Quando scatta
Prima di qualsiasi brainstorming, design, PRD o pitch per un nuovo prodotto o una feature
significativa: se non esiste `docs/discovery-brief.md` con `meta.phase: approved` nello stato,
applica prima la skill `product-discovery`.

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

## Regola ferrea (vale sempre, per ogni skill)
**Nessun artefatto di design, PRD, pitch o piano viene prodotto finché esistono
`pending_decisions` con `impact: high` e `resolved: false`.**
Criticare un'assunzione e poi costruirci sopra comunque equivale ad approvarla.

## Handoff contract verso la fase di design
Quando il design/brainstorming parte dopo la discovery, riceve `docs/discovery-brief.md` e:
- tratta i punti `confirmed` come vincoli non rinegoziabili;
- tratta le `pending_decisions` residue come domande da porre all'utente — MAI da risolvere da solo;
- non riapre decisioni presenti in `docs/decisions/decision-log.md` senza chiederlo esplicitamente;
- eredita le `assumptions` non validate come rischi dichiarati nel design, non come fatti.
