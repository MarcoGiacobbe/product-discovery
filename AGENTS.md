<!-- product-discovery:start -->
# Product Discovery Layer (fase 0, prima del design)

Vale per qualsiasi agente di coding. Le skill sono cartelle con `SKILL.md` (formato
agentskills.io). Se il runtime non le carica automaticamente, leggi il `SKILL.md`
corrispondente quando il trigger scatta e seguilo alla lettera (percorsi tipici:
`~/.claude/skills/`, `~/.agents/skills/`, o la copia nel repo).

## A inizio sessione (proattivo, non aspettare che l'utente lo chieda)
Se esiste `specs/discovery-state.yaml`, leggilo come prima cosa. Se `meta.phase` non è
`approved`, o `meta.feature_cycle` è attivo (non `null`/`done`), o esistono `pending_decisions`
con `resolved: false`: di' subito all'utente a che punto è la discovery (fase, decisioni aperte)
e proponi di riprendere dal punto esatto — senza aspettare che sia lui a nominarla.
Il comando `/discovery` forza in ogni momento avvio o ripresa dallo stato.

## Quando scatta
Prima di qualsiasi brainstorming, design, PRD o pitch:
- **nuovo prodotto**: se `specs/discovery-state.yaml` non esiste o non ha `meta.phase: approved`,
  applica prima `product-discovery` (modalità piena);
- **nuova feature con stato `approved`**: applica `product-discovery` in **modalità feature** —
  mini-discovery scopata alla feature: check sul decision log dai file (non a memoria),
  conflitti nominati subito, decisioni via `decision-gate`, delta-brief approvato in
  `docs/features/`. Nessun design con gate aperti, nemmeno condizionale.

## Source of truth
`specs/discovery-state.yaml` (dal template `specs/discovery-state.template.yaml`).
Ogni skill aggiorna solo le sezioni di cui è autrice. Si avanza solo quando il gate
d'uscita della skill corrente è soddisfatto.

## Regola ferrea (vale sempre, per ogni skill)
**Nessun artefatto di design, PRD, pitch o piano finché esistono `pending_decisions`
con `impact: high` e `resolved: false`.** Criticare un'assunzione e poi costruirci
sopra equivale ad approvarla.
**Target, scope, modello di business, mercato e canale sono SEMPRE `impact: high`:
l'impact non si autodeclassa per sbloccare il design.**

## Mappa degli artefatti (per riprendere in una nuova sessione)
Percorsi fissi per convenzione — niente indice da mantenere:
- `specs/discovery-state.yaml` — stato corrente; `meta.phase` e `meta.feature_cycle` dicono a che punto sei
- `docs/interviews/session-XX.md` — trascrizioni interviste (scrive: product-discovery)
- `docs/decisions/decision-log.md` — decisioni prese, append-only (scrive: decision-gate)
- `docs/discovery-brief.md` — brief di prodotto (scrive: pre-brainstorm-brief)
- `docs/features/NNN-<slug>-brief.md` — delta-brief per feature; NNN = progressivo a 3 cifre
  sui file esistenti (scrive: product-discovery, modalità feature)
- `spikes/` — codice usa-e-getta degli spike; i verdetti stanno in `evidence` dello stato

Per riprendere: leggi `meta.phase`, `meta.feature_cycle` e le `pending_decisions` con
`resolved: false` dallo stato. Lo stato è la verità — non ricostruirla dalla chat.
**Se lo stato si contraddice** (es. `phase: approved` con `pending_decisions` high non risolte,
o `resolved: true` senza `decided_by` valido): fermati, nomina le contraddizioni all'utente,
ripara solo riallineando al decision-log o tramite sua decisione esplicita. L'ambiguità non si
risolve MAI nella direzione che sblocca il task richiesto.

## Handoff contract verso il design
Il design/brainstorming (con Superpowers: `superpowers:brainstorming`; altrimenti il flusso
di design del runtime) riceve il brief pertinente — `docs/discovery-brief.md` per il prodotto,
`docs/features/NNN-<slug>-brief.md` per una feature — e:
- tratta i punti `confirmed` come vincoli non rinegoziabili;
- tratta le `pending_decisions` residue come domande da porre all'utente — MAI da risolvere da solo;
- non riapre decisioni presenti in `docs/decisions/decision-log.md` senza chiederlo esplicitamente;
- eredita le `assumptions` non validate come rischi dichiarati nel design, non come fatti;
- parte preferibilmente in una **sessione nuova**: tutto il necessario è su file, la cronologia
  della discovery non serve e non va usata (contesto lungo = più sycophancy, meno aderenza).
<!-- product-discovery:end -->
