[English](pygame-svg-icons-en.md) · **Italiano**

# Caso di studio: il prompt per passare da icone PNG a icone SVG in un gioco pygame

Una sessione reale su claude.ai, dalla richiesta alla consegna, riportata per intero. Serve a vedere come lavora la skill quando il prompt è per Claude Code: cosa legge, cosa chiede, cosa decide e cosa consegna.

| | |
|---|---|
| Data | 3 ottobre 2026 |
| Superficie | Chat su claude.ai, fuori da qualunque progetto |
| Modello | Claude Opus 5.5, effort Medium |
| Skill | `writing-prompts` 1.54, la versione inglese. La richiesta è in inglese; domande e consegna sono in italiano perché l'account ha l'italiano come lingua preferita |

**Cosa è stato modificato rispetto alla sessione.** Due punti, anonimizzati e segnati fra parentesi quadre: i titoli delle voci di memoria lette, e la prima opzione della domanda 9, che nominava un progetto dell'utente trovato in memoria. Il resto è trascritto senza ritocchi, compresi «slot» e le formulazioni della skill. Il prompt consegnato **non è stato eseguito**: la skill consegna il prompt e si ferma lì.

---

## 1. La richiesta

> Write me a prompt for Claude Code that helps me replace the PNG icons in a pygame program with SVG icons that stay sharp at every zoom level

## 2. Prima delle domande: cosa ha letto

Il riepilogo del ragionamento visibile in chat, nell'ordine:

- Preparando un prompt per Claude Code.
- Loaded skill — `writing-prompts`
- Identifying the relevant project to address the request.
- Read memory — *[due voci della memoria dell'utente]*
- Read `surfaces.md`
- Read `models.md`
- Listing clarifying questions about tools, scope, and project setup.

La skill si è aperta da sola sulla richiesta. Prima di chiedere ha letto la memoria dell'utente e due file: `surfaces.md`, con le regole per Claude Code, e `models.md`, con i delta per Opus.

## 3. L'intervista: undici domande in tre blocchi

Le domande arrivano nel pop-up interattivo della chat. Ogni domanda ha anche un campo libero («Something else»).

![Il pop-up della chat: la quarta domanda del primo blocco](img/pygame-svg-block-1.png)

In grassetto la risposta scelta. La colonna «Casella» è una **nota del curatore**: indica quale delle dieci caselle della skill riempie la domanda.

**Blocco 1 di 3**

| # | Domanda | Opzioni | Casella |
|---|---|---|---|
| 1 | Su quale modello girerà Claude Code? | **Opus** · Sonnet · Altro | 7 |
| 2 | Che forma deve avere il risultato della sessione Claude Code? | Modifiche + riepilogo *(codice modificato in place, poi un riepilogo in italiano di cosa è cambiato e perché)* · Branch git separato *(lavoro su un branch dedicato, main intatto)* · **Prima piano, poi codice** *(si ferma dopo il piano e aspetta il tuo ok)* · Scegli tu | 1 |
| 3 | Quali PNG vanno sostituiti? | Solo le icone UI *(pulsanti, simboli, indicatori; sprite, sfondi e foto restano PNG)* · **Tutti i PNG** *(ogni immagine raster del programma)* · Lista che fornisco io · Scegli tu | 2 |
| 4 | Cosa intendi per 'zoom'? | Ridimensionamento finestra *(l'interfaccia scala quando la finestra cambia dimensione o va a schermo intero)* · **Zoom in-app** *(comando interno, es. Ctrl +/−, che ingrandisce l'interfaccia)* · Scaling DPI Windows *(125%, 150%, 200% di Windows)* · Scegli tu | 3 |

**Blocco 2 di 3**

| # | Domanda | Opzioni | Casella |
|---|---|---|---|
| 5 | Con cosa rasterizzare gli SVG? Proposta: pygame-ce, perché carica SVG già alla dimensione richiesta senza DLL esterne (cairosvg su Windows richiede Cairo, fragile da installare e da impacchettare). | pygame-ce nativo *(pygame.image.load_sized_svg, nessuna dipendenza esterna)* · resvg / pynanosvg *(libreria dedicata, pygame resta quello attuale)* · cairosvg *(resa molto fedele, ma richiede le DLL di Cairo su Windows)* · **Scegli tu** | 4 |
| 6 | Che intervallo di zoom deve coprire? | 50–400% · **100–300%** · Quello già nel codice *(Claude Code lo legge dal programma)* · Scegli tu | 5 |
| 7 | I PNG originali dopo la migrazione? | Fallback automatico *(se un SVG manca o non si carica, si usa il PNG)* · **Eliminati** *(solo SVG, i PNG escono dal progetto)* · Archiviati in cartella *(spostati fuori dall'uso ma conservati)* · Scegli tu | 2 |
| 8 | Se per un'icona non esiste l'SVG o la libreria non si installa, Claude Code cosa fa? | Segnala e prosegue *(elenca i casi in sospeso e completa il resto)* · Si ferma e chiede · **Vettorizza dal PNG** *(prova a convertire il PNG in SVG automaticamente)* · Scegli tu | 9 |

![Il pop-up della chat: la prima domanda del secondo blocco, con la proposta motivata nel testo](img/pygame-svg-block-2.png)

Dopo il secondo blocco il ragionamento visibile riporta: *Valutando i rischi della vettorizzazione automatica dei PNG eliminati.* Poi arriva un terzo blocco.

**Blocco 3 di 3**

| # | Domanda | Opzioni | Casella |
|---|---|---|---|
| 9 | Su quale programma si lavora? | *[un progetto dell'utente trovato in memoria]* · **Un gioco Pygame** · Altro progetto | 10 |
| 10 | Gli SVG delle icone esistono già? | **Sì, tutti** · Solo alcuni · No, nessuno *(vanno ottenuti tutti: vettorizzazione o ridisegno)* | 10 |
| 11 | Il programma viene distribuito come eseguibile? | **Sì, PyInstaller .exe** · Altro packager · No, solo sorgente .py | 10 |

Dopo le risposte, il ragionamento visibile riporta: *Verifica della copertura completa degli asset grafici del gioco — Scelta delle librerie per il rendering vettoriale e la cache — Stesura del prompt tecnico in italiano per Claude Code — Definizione delle fasi di verifica, approvazione e pulizia finale.*

## 4. La consegna

Riportata per intero, nell'ordine in cui è arrivata.

**Assunzioni rimaste (rispondi sì/no; in ordine di impatto):**

1. Libreria (delegata a me): pygame-ce con load_sized_svg, che rasterizza l'SVG direttamente alla dimensione richiesta senza DLL esterne. Richiede di sostituire pygame con pygame-ce nel progetto.
2. La dimensione al 100% è quella in pixel dei PNG attuali.
3. Verifica a tre livelli di zoom (100, 200, 300%), presi dagli estremi e dal centro del tuo intervallo.
4. Eliminazione dei PNG solo dopo la verifica, con un commit git prima e una tua conferma sull'elenco dei file. Hai scelto di eliminarli, ma resta un'azione difficile da annullare.
5. La vettorizzazione dal PNG resta solo un ripiego, perché gli SVG esistono già tutti. Scelgo io il pacchetto, con controllo che esista su PyPI.

**Il prompt:**

```
Lavori su un gioco in Python/Pygame su Windows 11. Sostituisci tutte le immagini PNG del gioco con i corrispondenti SVG, rasterizzati alla dimensione effettiva a schermo, in modo che restino nitidi a ogni livello dello zoom interno del gioco (intervallo 100–300%). Oggi i PNG vengono ingranditi come bitmap e si sgranano: l'obiettivo è che ogni immagine venga generata dal vettoriale alla risoluzione richiesta, non scalata.

<contesto>
- Gli SVG esistono già per tutte le immagini: individua dove sono e abbinali ai PNG. Se un abbinamento è ambiguo, segnalalo nel piano.
- Il gioco è distribuito come .exe con PyInstaller: la soluzione deve funzionare anche nell'eseguibile, senza DLL da installare a mano.
- Chi ti guida non scrive codice: legge il piano e i riepiloghi. Scrivi in italiano, spiega cosa fa ogni modifica e perché, e definisci i termini tecnici la prima volta che li usi.
</contesto>

<approccio_tecnico>
- Libreria: pygame-ce, che con pygame.image.load_sized_svg rasterizza un SVG direttamente alla dimensione richiesta. Verifica quale pygame è installato e che la funzione esista nella versione disponibile. Il passaggio a pygame-ce richiede di disinstallare prima pygame, perché i due pacchetti sono in conflitto. Se pygame-ce non è praticabile, proponi un'alternativa nel piano spiegando perché, invece di cambiare strada in silenzio.
- La dimensione di riferimento al 100% è quella in pixel del PNG attuale. A zoom z l'immagine va rasterizzata a round(base × z).
- Rasterizza solo quando cambia lo zoom, mai a ogni frame. Tieni una cache per (immagine, dimensione) e libera le dimensioni non più in uso, così memoria e frame rate restano stabili.
- Lascia invariati trasparenze, posizioni, hitbox e ancoraggi: cambia la sorgente dell'immagine, non la logica del gioco.
- Se un SVG risulta mancante o non si carica, crealo vettorizzando il PNG. Prima verifica che il pacchetto di vettorizzazione scelto esista su PyPI e sia quello atteso. Elenca queste immagini nel riepilogo, perché la loro qualità va controllata a occhio.
</approccio_tecnico>

<modo_di_lavorare>
1. Leggi il codice e produci un piano: dove vengono caricati i PNG, come funziona oggi lo zoom, quali file cambieranno, l'abbinamento PNG→SVG con i casi dubbi, le dipendenze da aggiungere e le modifiche allo .spec di PyInstaller. Poi fermati e aspetta la mia approvazione.
2. Dopo l'approvazione implementa. Il lavoro si limita alla migrazione: niente refactoring, nuove funzionalità o commenti su codice che non tocchi.
3. Avvia il gioco a 100%, 200% e 300% e controlla che ogni immagine venga caricata dall'SVG. Ricostruisci l'.exe e controlla che parta e mostri le immagini.
4. Solo dopo questi controlli elimina i PNG sostituiti. Prima assicurati che lo stato attuale sia salvato in un commit git (se il progetto non è sotto git, chiedimelo) e chiedimi conferma mostrandomi l'elenco dei file.
5. Cancella i file temporanei creati per i test.
</modo_di_lavorare>

Scrivi una frase prima di iniziare e aggiorna solo per scoperte rilevanti o cambi di strada. Chiudi con un riepilogo breve: esito, file cambiati, immagini vettorizzate da controllare e problemi aperti.
```

**Destinatario e superficie:** Claude Code, modello Opus. Avvia la sessione dalla cartella del gioco, così Claude Code legge il codice e lo .spec di PyInstaller. Al punto 1 il piano arriva prima di qualunque modifica. Controlla soprattutto l'abbinamento PNG→SVG e il passaggio a pygame-ce.

**Tecniche usate:**

- Direttiva + contesto sugli obiettivi (famiglia A, p. 5, 39): spiega perché i PNG sgranano, così il modello capisce da solo cosa conta come «nitido».
- Scope (famiglia A): il punto 2 vieta il refactoring. Delta Opus da `models.md`: Opus tende ad allargare il compito.
- Plan-and-Solve con piano come artefatto da approvare (famiglia D, p. 14): risponde alla tua scelta «prima piano, poi codice».
- Blocchi Claude Code da `surfaces.md`:
  - niente over-engineering;
  - conferma prima delle azioni distruttive (eliminazione dei PNG);
  - controllo che i pacchetti esistano su PyPI, per evitare nomi inventati o malevoli (p. 30);
  - pulizia dei file temporanei.
- Delta Opus da `models.md`:
  - cadenza della narrazione descritta in positivo;
  - riepilogo finale breve;
  - nessuna istruzione generica di «ricontrollare». Il punto 3 è un collaudo con criteri concreti, non un'autoverifica generica.

**Slot che non sono diventati domande:**

- Slot 8 (chi legge l'output): sei tu, che non scrivi codice. Viene dal profilo, da cui deriva la riga sulle spiegazioni in italiano. Va nel prompt perché le tue preferenze di claude.ai non sono attive in Claude Code.
- Slot 7 (superficie): Claude Code, dalla tua richiesta.
- Tutti gli altri slot vengono dalle tue risposte. Fanno eccezione libreria e strumento di vettorizzazione, che mi hai delegato: sono le assunzioni 1 e 5.

---

## Cosa si vede in questo caso

*Nota del curatore: fatti osservabili nella sessione, senza giudizi sul prompt.*

- **Il destinatario detto non si richiede.** La richiesta nomina Claude Code, quindi la skill chiede solo il modello. Nel [caso della bilancia](analytical-balance-it.md), dove il destinatario non era detto, le prime due domande erano destinatario e modello.
- **La superficie cambia i file letti e il prompt.** Prima delle domande la skill apre `surfaces.md`. Dal file vengono il piano da approvare, la conferma prima di cancellare, il controllo dei pacchetti su PyPI e la pulizia finale.
- **La memoria si legge ma non si dà per scontata.** In memoria c'era un progetto dell'utente che poteva essere quello giusto. La skill lo propone come prima opzione della domanda 9, ma chiede invece di assumerlo.
- **Le definizioni prima dei numeri.** «Zoom» ha tre significati diversi in un programma per Windows (finestra, comando interno, scaling DPI), e la domanda 4 lo fissa prima che la 6 chieda l'intervallo.
- **Un blocco nato da due risposte.** Le risposte 7 e 8 insieme dicevano: elimina i PNG, e se manca un SVG ricavalo dal PNG. La skill nota il rischio e apre un terzo blocco, che chiede se gli SVG esistono già. Con «Sì, tutti» la vettorizzazione resta un ripiego (assunzione 5), e i PNG si eliminano solo dopo il collaudo, con un commit git e una conferma (assunzione 4).
- **Fatti senza «Scegli tu».** Le domande del terzo blocco riguardano il caso dell'utente: quale programma, quali file esistono, come si distribuisce. Non hanno «Scegli tu»: sono fatti che conosce solo l'utente.
- **La delega finisce in ricevuta.** La domanda 5, delegata con «Scegli tu», ricompare come assunzione 1, con la scelta fatta e la sua conseguenza: sostituire pygame con pygame-ce.
- **Il profilo entra nel prompt solo quando serve.** Nella bilancia le preferenze dell'utente non erano ripetute, perché la chat le applica già. Qui sì, perché in Claude Code non sono attive.
