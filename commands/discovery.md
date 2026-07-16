---
description: Avvia o riprendi la product discovery dallo stato su file
---

Avvia o riprendi il processo di product discovery. Passi:

1. Leggi `specs/discovery-state.yaml`. Se non esiste, invoca la skill `product-discovery` (modalità piena) e parti dal setup.
2. Se esiste, riferisci all'utente in 3 righe: `meta.phase`, `meta.feature_cycle`, quante `pending_decisions` con `resolved: false` (e quali high).
3. Instrada in base allo stato — invoca la skill, non emularla:
   - `phase: discovery` o materiale intervista incompleto → `product-discovery`
   - `phase: redteam` o assunzioni `confidence: low|med` non validate → `discovery-redteam`
   - `pending_decisions` non risolte → `decision-gate`
   - decisioni high risolte, brief mancante → `pre-brainstorm-brief` (o delta-brief feature se `feature_cycle` attivo)
   - `phase: approved` e nessun ciclo attivo → chiedi: nuova feature (modalità feature) o handoff al design?
4. Argomento opzionale: se l'utente ha scritto `/discovery <idea o feature>`, trattalo come pitch e parti dal punto giusto.

Lo stato su file è la verità — non ricostruirla dalla cronologia della chat.
