# Handoff FitPro: riprendere le 9 modifiche

**Prima cosa da fare:** l'utente ha interrotto la sessione precedente chiedendo di riprendere le modifiche nella sessione successiva. Non modificare file finché non ti dà il via. Salutalo in una riga, conferma che le 9 modifiche qui sotto sono ancora tutte da fare e chiedi se procedere.

## Contesto
FitPro è la PWA di Andrea (italiano, non programma) per allenamenti di calisthenics e powerlifting. Risposte in italiano, brevi e dirette, con passaggi semplici. Tutta l'app è in un file.

- Cartella: `/Users/andrea/Documents/CCode/FitPro` (non è un repository git).
- File: `index.html`, `sw.js` (cache ora `fitpro-v18`, incrementare a ogni modifica), `manifest.webmanifest`, `icon-192.png`, `icon-512.png`, `icon-maskable-512.png`. Non pubblicare `.claude/` e i file `anteprima-*`.
- Anteprima: server `fitpro` in `.claude/launch.json` (porta 8765). Prova in emulazione mobile 375x812. Il service worker serve copie vecchie: prima di verificare, deregistralo e svuota la cache. Gli screenshot a volte sono in ritardo: conferma via JS.
- Il pannello browser può contenere una sessione di prova avviata dall'utente: non concluderla, non chiamare `localStorage.clear()`. Dopo i test ripristina i dati.
- Pubblicazione: l'utente carica a mano su https://github.com/L0racolo/Fitpro/upload/main. Non risulta se abbia già pubblicato: chiediglielo solo quando serve. Non pubblicare senza conferma.
- Stile attuale: "Fitness" (ispirato a Fitness di iOS) predefinito, "Classico" alternativo. Accento = variabili CSS `--e-400/500/600`.

## Stato
Le modifiche del turno precedente sono state RIFIUTATE: nessuna delle 9 è applicata. Il codice è quello della v18. Rileggi `index.html` prima di modificarlo.

## Le 9 richieste (testo dell'utente in sintesi) e come applicarle
1. **Header Home: logo vecchio da sostituire con il nuovo; togliere il cognome.** Usa `<img src="icon-192.png">` al posto del riquadro col fulmine. Togli il badge MAMMANA (`#header-badge`, anche da `updateRestPill`). "Andrea Mammana" diventa "Andrea": `DEFAULT_APP_DATA.athlete`, dati già salvati (in `migrateData`, con un nuovo flag), `<title>`, nome nel manifest, testi del reset, nome del backup. NON cambiare `STORAGE_KEY`: conterrebbe la perdita dei dati.
2. **Schede: "Aggiungi nuovo esercizio alla sessione" va alla fine.** Spostalo dopo `#session-list`, cioè dopo l'ultima card. Dillo all'utente, perché "alla fine della card esercizio" potrebbe voler dire dentro ogni card.
3. **Titolo card senza il numero di serie attive.** Togli la riga "N serie attive".
4. **Eliminare il resoconto settimanale (Sett 1, Sett 2...).** Rimuovi `progressionHTML` e i suoi usi (card sessione e righe delle schede), e il parametro `withProgression` di `routineRowHTML`. Tieni i campi "Progressione 6 settimane" nella modifica esercizio: le percentuali lì dentro calcolano i carichi dai massimali. Segnalalo.
5. **Eliminare preset recupero.** Rimuovi `renderExerciseRestPresetsHTML` e `setExerciseRestPreset`.
6. **Eliminare l'avvio automatico del timer.** Togli `onchange="startRestTimer(...)"` dalle reps. Senza 5 e 6 il timer non partirebbe più: durante la sessione in Schede tieni sempre visibile `#header-rest-pill` (solo ⏱ da fermo, conto alla rovescia in corso). Il tocco apre il pannello `#rest-timer-banner` con i tempi 1-5 min; da fermo nascondi +30s, pausa, stop e barra. I beep restano. Rimuovi anche i campi "Recupero" da entrambe le modali esercizio e `targetRestSec`, ormai senza effetto, e dillo all'utente.
7. **Invertire le caselle:** prima REPS, poi KG / ZAV.
8. **Isometrie in secondi** (tuck planche, front lever). Aggiungi `unit: 'sec'` a `ex-101, ex-102, ex-301, ex-302` e, perché la sua progressione è in secondi, `ex-205`; segnala quest'ultimo. Migrazione con flag nuovo in `migrateData` (oggi esce subito se `removed2023` è vero: ristruttura). Etichetta casella SEC, righe scheda con `"` (es. `6 × 5-8"`), selettore "Si misura in" Ripetizioni/Secondi nelle due modali, unità salvata in sessione e storico, finestra del calendario con i secondi. I secondi non entrano nel volume totale in kg. Helper previsti: `sessionUnit(ex)` (`ex.unit` oppure `findExercise(ex.exId).unit`) e `targetLabel(ex)`.
9. **Opzioni: altri colori, tra cui arancione e viola.** Selettore "Colore" sotto "Stile dell'app": Lime (predefinito; "Verde" con il Classico), Arancione, Viola, Azzurro, Rosso, Rosa. Salva in `settings.accent`; `html[data-accent="..."]` ridefinisce `--e-*`, dichiarato dopo i blocchi di stile; imposta l'attributo nello script in testa per evitare lampi di colore. Il testo sui pulsanti pieni è scuro: usa tinte `--e-500` chiare. Con Arancione cambia `--a-*` in giallo, altrimenti si confonde con le settimane di scarico in ambra. L'icona dell'app resta lime: segnalalo.

Valori previsti (`--e-400` / `--e-500` / `--e-600`, RGB):
- Arancione: 255 165 60 / 255 149 0 / 175 80 0
- Viola: 196 160 255 / 179 136 255 / 109 62 217
- Azzurro: 90 200 250 / 90 200 250 / 0 110 190
- Rosso: 255 120 110 / 255 95 85 / 200 30 30
- Rosa: 255 140 190 / 255 120 180 / 200 30 110

## Alla fine
Incrementa `sw.js` a `fitpro-v19`, prova tutti i punti in emulazione (anche dati vecchi, sessione ripristinata, tema chiaro), controlla la console, poi indica i file da caricare: `index.html`, `sw.js`, `manifest.webmanifest` e le 3 icone se non ancora pubblicate. Ricorda il backup JSON e, per vedere la nuova icona su iPhone, di rimuovere e riaggiungere l'app da Safari (i dati possono andare persi: reimportare il backup).

## Da segnalare all'utente
- Le reps partono da 1 e, senza le settimane, la sessione non mostra più l'obiettivo: proponi di partire da `repMin`, senza farlo di tua iniziativa.

## Ancora aperti, da non fare senza richiesta
- Il primo messaggio sullo stile si interrompeva dopo "Nel": non è mai arrivato il seguito.
- Pulizia dall'audit (`attachSwipeListenersLegacy`, `toggleSetComplete`, `selectMesocycleWeek`, `setCycleFromWeek`, ecc.): l'utente ha detto "non ora".
- Carichi iniziali senza percentuale (Panca 60, Military 35...): da azzerare solo se l'utente conferma che sono del 2023.

## Skill suggerite
- `run` per avviare e provare l'app.
- `anthropic-skills:built-in-browser` prima di usare il pannello browser.
- `frontend-design:frontend-design` per il selettore colori.
