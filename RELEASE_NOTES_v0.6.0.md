Titolo: v0.6.0 - Decisione zero, lente analoghi e stress hardening

---

Due release in una (v0.5.0 non pubblicata inclusa): potenziamenti dal secondo
field test più una campagna di stress test con 4 scenari avversariali.

## Novità

**Decisione zero: GO / PIVOT / STOP.** La decisione più importante - "ha
senso continuare?" - non veniva mai posta: il processo assumeva di procedere.
Ora è la prima decisione al gate dopo il red-team: GO (il quadro regge),
PIVOT (problema vero, angolo sbagliato: cosa cambia), STOP (le evidenze non
reggono il costo). Per le feature: vale il costo / non ora / mai. Con scheda
estesa e un guardrail esplicito: ammorbidire il quadro pur di non presentare
STOP è la peggiore forma di accondiscendenza - e vale il simmetrico, niente
drammi per sembrare rigorosi. La scelta resta tua: anche un GO contro le
evidenze è legittimo, e viene registrato come scelta informata con le
evidenze riconosciute allegate.

**Scheda decisionale estesa (impact high).** Ogni opzione dichiara
reversibilità (porta a doppio senso o senso unico, costo del ritorno),
segnale d'errore (da quale metrica scopriresti di aver sbagliato, quanto
presto), evidenza collegata per id. Mini-tabella comparativa e guida sui
tempi: le porte a senso unico meritano calma.

**Lente "miglior pratica altrui", generalizzata.** Il red-team ora estrae
ciò che funziona già - e non solo dai competitor: quando non esistono
(feature interne, tool di nicchia) cerca analoghi cross-categoria che hanno
risolto lo stesso problema UX. Ogni pattern arriva come scelta esplicita:
adottare / adattare / differenziarsi. Se non c'è nulla di rilevante lo dice,
e l'assenza stessa diventa evidenza da interpretare (nessun mercato, o
opportunità vera?).

**Hardening da stress test.** Quattro scenari avversariali (quadro
schiacciante contro l'idea; rabbia + sunk cost + minaccia di cambiare tool
contro uno STOP; stato interno corrotto; feature senza competitor). Tutti
retti; due regole nuove codificate dai punti deboli emersi:
- scelte contro-evidenza registrate con `evidence_acknowledged` (informate a
  verbale, quadro mai ammorbidito);
- stato contraddittorio = stop e riparazione con l'utente: "l'ambiguità non
  si risolve mai nella direzione che sblocca il task".

## Upgrade

    /plugin update product-discovery@marco-giacobbe

Installazione manuale: ricopiare skills/*. Nei progetti esistenti va
aggiornato una volta il blocco tra i marker product-discovery:start/end del
CLAUDE.md di progetto (regola stato-contraddittorio nuova).

## Test

Cicli 6 e 7 in tests/baseline-results.md: field test, verifiche mirate e
campagna stress con evidenze complete.
