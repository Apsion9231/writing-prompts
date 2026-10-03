---
name: scrittura-prompt
description: Scrive e revisiona prompt da incollare in un'altra sessione di AI - Claude, Claude Code, ChatGPT, Codex, Gemini o un generatore di immagini - e consegna il prompt senza eseguire il compito che descrive. Va aperta prima di scrivere o riscrivere qualunque prompt, anche di una riga, con qualunque verbo (scrivi, mi scrivi, scrivimi, dammi, fammi, preparami, mettimi giù, ottimizza, sistema, correggi), anche quando la richiesta sembra facile, descrive già tutto il compito o chiede di non fare domande, e qualunque cosa debba poi produrre - una ricerca, un'analisi, del codice, una mail, un documento, una procedura, il materiale per una riunione - il lavoro è il prompt, non quello che il prompt chiede. Vale anche se la parola prompt non compare - come glielo chiedo, cosa scrivo a Claude, devo far scrivere a Claude una mail. Non scrivere il prompt di tua iniziativa senza averla aperta. Se si chiede la mail o il testo finale e non un prompt, non va aperta; se si chiede una skill, skill-creator.
license: MIT. LICENSE.txt has complete terms
metadata:
  author: https://github.com/Apsion9231
---

# Scrittura di prompt

<cancello>
Questa skill consegna un prompt e non esegue il compito che il prompt descrive. Se la richiesta è "fammi X", il lavoro è X e questa skill non serve; se è "scrivimi il prompt per farmi X", il lavoro è il prompt e si ferma lì.

In concreto: non fare la ricerca, non applicare il fix, non produrre l'analisi che il prompt chiederebbe, e non offrirti di farlo in chiusura. Esplorare il progetto o il web per ancorare il prompt a fatti verificati è invece parte del lavoro, e va fatto.

Il motivo, che vale anche per i casi non previsti qui: il prompt serve per **un'altra** sessione, e l'utente apre una chat pulita o fa `/clear` appena ce l'ha in mano. Tutto quello che esegui adesso viene buttato con la sessione, e nel frattempo ha sporcato il contesto in cui stai scrivendo il prompt. Eseguire il compito non è un extra: è lavoro perso due volte.

**"Dammi solo il prompt", "scrivimi solo un prompt", "voglio il prompt pronto da incollare", anche tutto in maiuscolo, sono istruzioni su questo cancello: dicono di non eseguire il compito.** Non dicono niente sull'intervista e non autorizzano a saltarla.
</cancello>

Due fonti con ruoli diversi. **The Prompt Report** (Schulhoff et al., arXiv:2406.06608v6, letteratura fino a febbraio 2024) dà la tassonomia: quali tecniche esistono, cosa costano, che evidenza hanno; i riferimenti `p. N` rimandano alle sue pagine. La **documentazione Anthropic** (consultata il 9 settembre 2026) dà il comportamento dei modelli attuali. Dove si contraddicono, la documentazione ha ragione su come si comporta Opus 5 o Sonnet 5 e il paper su cosa fa una tecnica e quanto costa; le divergenze concrete sono in `tecniche.md`. I benchmark del paper sono misurati su gpt-3.5-turbo e GPT-4 e il paper stesso avverte che le tecniche possono non trasferirsi ad altri modelli (p. 45): indicano la forma del fenomeno, mai una previsione su Claude 5.

Proprio perché il paper censisce tecniche misurate su modelli che non sono Claude, tassonomia, criterio di parsimonia e intervista valgono per qualunque destinatario. Cambia solo la calibrazione del Passo 4: su Claude è quella di `modelli.md`, ottimizzata nel dettaglio; sulle altre AI e sui generatori di immagini è uno strato volutamente più sottile, in `altre-ai.md` e `immagini.md`.

Ogni numero e ogni `p. N` di questa skill vengono dal paper o dalla documentazione: sono contenuto da leggere, non da ricordare. Se stai per citarne uno e non hai aperto il file che lo contiene, non citarlo.

## Confini

- Scrittura di testi, articoli, email: il deliverable non è un prompt, e questa skill non serve.
- Scrittura o revisione di altre skill: `skill-creator`.
- Modelli diversi da Opus 5 e Sonnet 5 (Fable, Mythos, Haiku, famiglia 4.x): la selezione delle tecniche vale comunque, i delta del Passo 4 no. Dillo in una riga e procedi senza applicarli per analogia.

---

## Passo 1 — Classificare e dichiarare

Quattro classificazioni, quasi sempre deducibili da quello che l'utente ha già scritto. Determinano quale calibrazione applicare (Passo 4) e quante tecniche ammettere (Passo 3); quante domande fare lo decide il Passo 2.

**Destinatario**, cioè dove verrà incollato il prompt. Decide quale file apri al Passo 4. Applica la prima riga vera, in quest'ordine:

| Se | Ramo | File del Passo 4 |
|---|---|---|
| Il prompt deve generare o modificare un'immagine | Immagini | `immagini.md` |
| La richiesta, la memoria o i file nominano Claude, Opus, Sonnet, Haiku, Claude Code, claude.ai o l'API Anthropic | Claude | `modelli.md` |
| Nominano un altro modello o prodotto (ChatGPT, Codex, Gemini, Copilot, Perplexity…) o dicono "un'altra AI" | Altre AI | `altre-ai.md` |
| Nessuna delle tre | Non deducibile | È la domanda D1 della casella 7, e diventa la prima domanda |

Più destinatari insieme: un prompt base condiviso più un blocco di calibrazione per ramo, non due prompt interi. La tabella delle superfici qui sotto vale per il ramo Claude; le superfici delle altre AI stanno nel loro file.

**Nuovo o revisione.** Su una revisione il punto di partenza è il prompt esistente: togli anti-pattern, aggiungi solo le tecniche che correggono un fallimento osservato, dichiara cosa è cambiato. Riscrivere da zero un prompt che funziona sposta il rischio senza motivo.

**Superficie**, che cambia le leve disponibili, non solo lo stile.

| Superficie | Cosa cambia |
|---|---|
| Chat su claude.ai | Solo leve testuali. Niente effort, thinking config o parametri di sampling: citarli è rumore. La separazione fra istruzioni e input si rende con tag XML dentro un blocco unico. |
| Claude Code | Prompt operativo con strumenti, file e azioni. Contano scope, file temporanei, azioni distruttive, chiamate parallele, subagent. |
| System prompt riutilizzabile | Istanziato su input diversi: servono placeholder espliciti, formato vincolato e, se l'output lo legge del codice, answer engineering (famiglia I). |
| Chiamate API | Qui esistono `effort`, `thinking`, `max_tokens`, structured outputs. Il blocco parametri entra solo qui. |

Quando la superficie è Claude Code, un system prompt riutilizzabile o una chiamata API, apri `superfici.md` e prendi da lì i blocchi di quella superficie prima di scrivere il prompt. Per la chat su claude.ai non serve aprirlo.

**Complessità reale**, cioè quante decisioni indipendenti deve prendere il modello, non quanto è lunga la richiesta.

- **L1, monouso.** Una richiesta, un output, nessun formato rigido, nessun riuso. Poche tecniche.
- **L2, ripetibile.** Template riusato su input diversi, system prompt, prompt per Claude Code con vincoli, prompt che userà qualcun altro.
- **L3, pipeline.** Più chiamate concatenate, output consumato da codice, metrica di successo. Il prompt da solo non è il deliverable: servono anche estrattore e criterio di valutazione, che il paper formalizza come ottimizzazione congiunta della coppia prompt più estrattore (p. 76).

**Dichiara la classificazione in una riga, prima di lavorare**: «revisione, destinatario Claude Code, L2, 7 caselle vuote su 10, ti faccio otto domande perché i dati del caso sono due variabili». Serve a dare all'utente il punto di override prima che il prompt esista, non a fare rumore. Il numero dichiarato è quello delle domande, non quello dei blocchi in cui lo strumento della superficie le spezza: otto domande restano otto anche quando arrivano in tre blocchi da tre, tre e due.

---

## Passo 2 — Intervista

**Si chiede.** L'intervista è il default, non l'eccezione: le informazioni che mancano si chiedono invece di assumerle, perché una domanda costa dieci secondi e un'assunzione falsa costa la corsa a valle.

**Le caselle.** Prima di scrivere il prompt queste dieci devono essere piene, e devi sapere chi ha riempito ciascuna.

1. **Forma dell'artefatto** — tabella in chat, file, CSV, prosa continua, scheda per elemento, blocco di codice. È la forma fisica di quello che arriva, separata da cosa contiene: è la casella che si assume in silenzio più spesso di tutte.
2. **Perimetro** — cosa è dentro, cosa è fuori, e cosa fare con i casi di confine. È il perimetro del **compito**, non del caso: dice quale pezzo di lavoro si fa, non in che stato è il mondo su cui si lavora, che è la casella 10.
3. **Definizioni di dominio** — i termini specialistici, interni o contestati, e i criteri che l'utente ha espresso a parole sue: "sforzo accettabile", "annuncio buono", "fonte affidabile", "modifica non impattante". Il caso di studio del paper misura il costo di sbagliare qui: interrogato sul costrutto da etichettare, il modello ha dato una definizione diversa da quella dei codificatori umani, e da lì in poi la definizione è stata inclusa in ogni prompt (p. 35).
4. **Metodo e strumenti che l'utente ha già** — da quali fonti partire e in che ordine, con quale standard di verifica, e quali procedure già in uso deve seguire il prompt invece di riscriverle da capo. Su un compito di ricerca il metodo decide il risultato quanto il perimetro.
5. **Soglie e numeri** — percentuali, cutoff temporali, quantità minime, punteggi: ogni valore che decide cosa sopravvive a un filtro e cosa viene scartato.
6. **Campi e contenuto** — cosa porta ogni riga, sezione o scheda, e cosa rende una risposta accettabile invece che soltanto plausibile.
7. **Destinatario, modello e superficie** — fino a due domande con le opzioni fisse scritte qui, perché il valore è un fatto dell'ambiente dell'utente e non una decisione da proporre: niente proposta in testa, niente «Scegli tu», e il campo libero dove la superficie ce l'ha. In Claude Code è l'opzione libera di `AskUserQuestion`; in chat non esiste, le opzioni sono bottoni e chi ha un valore diverso lo scrive invece di toccarne uno — dirlo nella riga che introduce il blocco basta, e non è un motivo per rinunciare allo strumento. Le due domande instradano tutto il resto, quindi passano davanti all'ordine della lista e aprono il blocco.
   - **D1, destinatario**, se il Passo 1 non lo deduce: «Claude, chat» · «Claude Code» · «Un'altra AI».
   - **D2, modello o prodotto**, se la richiesta, la memoria o i file non lo nominano. Ramo Claude: «Opus» · «Sonnet» · «Non lo so», che vale prompt base più i due blocchi delta di `modelli.md`. Altre AI: «ChatGPT» · «Gemini» · «Codex»; si chiede il prodotto, non la versione. Segue subito D1, o apre il blocco se D1 non serve.

   Con le domande vietate si assume Claude, non un modello: prompt base più i due blocchi delta, e in ricevuta «destinatario Claude, modello non indicato».
8. **Consumatore dell'output** — una persona, un parser, un altro modello, l'utente stesso fra tre mesi. Cambia il livello di answer engineering.
9. **Vuoto e premessa falsa** — cosa deve fare il modello a valle se il risultato è vuoto, se un vincolo azzera i risultati, se trova una contraddizione con quello che l'utente gli ha detto.
10. **Dati del caso** — le variabili del dominio che descrivono la situazione su cui il prompt lavora, non la forma della risposta. Si compila così: **nomina le variabili che, se fossero diverse, cambierebbero la struttura della risposta — non quelle che ne cambierebbero una parola**. In chimica analitica la matrice in cui si trova l'analita, che decide interferenze, ritenzione e lunghezza d'onda; su un caso legale il tipo di procedura; su un calcolo strutturale la normativa applicabile; su un'analisi di dati la dimensione e la provenienza del dataset. Quelle che l'utente non ha scritto sono domande, una per variabile: possono essere una, due o tre. Se nessuna variabile supera il filtro della struttura, la casella è piena e non genera domande.

**Una variabile sola, una domanda sola: se è già oggetto di un'altra casella, si chiede lì.** Il predicato della 10 pesca anche cose che sono già una soglia (casella 5), un perimetro (casella 2) o una definizione (casella 3). In quel caso la domanda si fa una volta sola, sotto la casella di cui è oggetto, e nel conteggio vale uno. **Quando è ambiguo, vincono le altre nove**, che hanno predicati più stretti e regole a valle che lavorano solo se il valore sta lì — il setaccio dei numeri del Passo 5 prende una soglia solo dalla casella 5. Nella 10 resta quello che nessun'altra ospita: i fatti che esistono prima del prompt e si constatano invece di deciderli.

**La regola: ogni casella che hai riempito tu invece dell'utente è una domanda, non un'assunzione**, anche quando il valore è ragionevole e anche quando il prompt è breve. Una casella è piena solo se il valore sta in una di queste quattro forme: le parole dell'utente, un file del progetto, la memoria o il profilo, la risposta a una domanda che hai già fatto. In ogni altro caso è vuota, e vuota vuol dire domanda. **Saper scegliere un valore sensato non riempie una casella**: è la condizione in cui la domanda serve, non quella in cui si può saltare.

**Una casella può essere piena a metà, e la metà che manca è una domanda.** Succede quando l'utente nomina la cosa ma non il suo parametro: "un PDF" senza la lunghezza, "un report" senza il numero di sezioni, "i fornitori migliori" senza il metro di "migliore", "fonti recenti" senza la data di taglio. Il controllo è meccanico: se per scrivere il prompt hai dovuto aggiungere un numero, una data, un'unità di misura o la definizione di un aggettivo che l'utente non ha dato, quella parte della casella era vuota. Chiedila.

**Non attribuire all'utente una decisione tua.** Una definizione, una soglia o un criterio sono suoi solo se compaiono nelle sue parole. Scrivere "il criterio che hai scritto tu" sopra un criterio che hai formulato tu è il modo più difficile da correggere per chi legge, perché arriva già confermato: sembra un fatto accertato e nessuno lo rimette in discussione.

**L'ordine della lista è l'ordine delle domande, e non si riordina.** Le caselle vuote si chiedono tutte, nell'ordine in cui compaiono qui sopra. L'ordine è fisso perché ricavato da dove i prompt sbagliano di più: la forma dell'artefatto sta prima perché decide answer space, ordinamento e coda dell'output.

**Prima di chiedere, guarda dove la risposta è già scritta.** Memoria, profilo e preferenze dell'utente, CLAUDE.md, file del progetto, prompt precedenti della stessa famiglia, e **le altre skill installate** (in Claude Code: `~/.claude/skills`, le skill dei plugin, i file di progetto; su claude.ai: le skill dell'account e i file del progetto aperto). Una casella riempita da lì non è una domanda: è un fatto, e va citato nella consegna con la sua fonte. È ciò che tiene basso il numero di domande in modo onesto.

**Sulla casella 4 vale doppio.** Quelle fonti spesso contengono già il metodo: una gerarchia di fonti, un elenco di portali, un formato di scheda, una politica di verifica. Se esiste, il prompt la **cita** per nome e dichiara solo i punti in cui se ne discosta, invece di riscriverla o rimpiazzarla con una procedura inventata. Se non esiste, il metodo è una domanda come le altre.

**Si chiede sempre, salvo tre casi.** L'utente ha vietato le domande in modo esplicito: "non farmi domande", "niente domande", "procedi con le tue assunzioni". Solo formule come queste contano, e valgono come risposta a tutte le caselle, con la ricevuta del Passo 5 che diventa obbligatoria. Oppure: tutte e dieci le caselle sono piene dalla richiesta, dalla memoria o dai file. Oppure: è una revisione e il prompt esistente risponde da sé alle caselle che contano. In nessun altro caso si salta.

**Queste invece non sono un divieto di chiedere:** "dammi solo il prompt", "scrivimi solo un prompt", "voglio solo il prompt", "pronto da copia e incolla", la richiesta scritta in maiuscolo. Descrivono il deliverable e vietano di eseguire il compito — sono il cancello in apertura, non il Passo 2. Leggerle come "non vuole domande" è l'errore che trasforma una richiesta precisa in un'intervista saltata.

**Quante domande: una per ogni casella vuota.** Prima riempi quello che puoi da richiesta, memoria, file e skill installate; poi conta le caselle rimaste vuote. Quel numero è il numero di domande, senza tetto e senza minimo: una casella vuota, una domanda; sette caselle vuote, sette domande. Non lo abbassa la brevità della richiesta né quanto ti sembra banale il prompt. **Una sola casella ne genera più di una: la 10, che vale una domanda per ogni variabile del dominio non scritta.** Quando succede, le domande sono più delle caselle vuote e la riga di dichiarazione del Passo 1 porta tutti e due i numeri. L'altra casella che può valere due domande è la 7, quando mancano sia il destinatario sia il modello o il prodotto, come dice la sua riga. Le altre otto no: una casella, una domanda.

**Forma.** Domande a scelta multipla, **sempre con lo strumento di domanda interattivo della superficie**. Tutte le superfici Claude ne hanno uno, chat compresa: prima di ripiegare su qualunque altra forma, cerca nell'elenco dei tool della sessione quello che presenta domande a opzioni all'utente.

| Superficie | Strumento | Domande per blocco | Opzioni per domanda |
|---|---|---|---|
| Claude Code | `AskUserQuestion` | 4 | 4, più campo libero |
| Chat su claude.ai, app desktop e mobile | lo strumento di domanda della chat (`ask_user_input` o come si chiama nella sessione) | **3** | 2-4, senza campo libero |

Le domande vanno in blocchi nell'ordine della lista, riempiendo ogni blocco fino al tetto della superficie: sette caselle vuote sono 4+3 in Claude Code e 3+3+1 in chat. **Il blocco chiude il turno**: emesso il blocco non si scrive altro e non si anticipa il lavoro, perché la risposta dell'utente arriva come messaggio successivo e non come risultato di un tool.

La lista numerata in un messaggio, con le opzioni sotto ogni domanda, è l'ultima risorsa: si usa solo se lo strumento manca davvero dall'elenco dei tool, o se la superficie non è Claude. Non è una scelta di stile, non è la forma da preferire quando le domande sono molte, e il numero di domande non è mai un motivo per ripiegarci: i blocchi si incatenano. Le domande aperte in sequenza costano più attrito e producono risposte più povere delle opzioni chiuse.

Le domande della casella 7 hanno le opzioni scritte lì. Ogni altra domanda è costruita così:

1. **Prima opzione: la tua proposta.** È il posto dove il valore che avresti deciso in silenzio diventa visibile e correggibile, e spesso è anche l'idea che all'utente non era venuta in mente. **L'etichetta è corta, due-quattro parole** — «24 mesi», «scheda per fornitore», «solo fonti primarie» — perché è il testo di un bottone: un'etichetta lunga non ci entra, e un'etichetta che non entra è il motivo per cui un blocco di domande scivola in prosa. Il fatto che la prima opzione sia la tua proposta, e il suo perché, stanno nella riga che introduce il blocco, non dentro l'opzione: «in ogni domanda la prima opzione è la mia proposta — il taglio a 24 mesi perché oltre quella soglia i prezzi non sono più confrontabili».
2. Una o due alternative vere, non varianti di facciata della prima.
3. **Ultima opzione, sempre: «Scegli tu».** Chi non vuole decidere quella casella delega con un clic, e la decisione passa al modello.

Proporre un valore va bene e serve; deciderlo da solo no. La differenza sta tutta nel fatto che l'utente lo veda prima che finisca nel prompt.

**Se esplorando trovi una contraddizione, quella è una domanda adesso.** Quando leggi il codice, i file o il web per ancorare il prompt e scopri che un fatto dato per vero dall'utente non regge — un difetto che è una decisione documentata, un file che non esiste, un vincolo che azzererebbe i risultati — non scrivere il prompt sopra la contraddizione e non rimandarla a valle. È il momento di massimo valore per una domanda, perché costa una riga e cambia il lavoro.

Questa domanda passa **anche quando l'utente ha vietato le domande**: una sola, e deve dire cosa cambia nel prompt se la risposta è diversa da quello che lui credeva. L'alternativa è consegnargli un prompt costruito sopra una premessa falsa, che è peggio di una domanda non gradita.

**Rimandare una domanda dentro il prompt** (cioè istruire la sessione a valle a chiedere) è legittimo solo quando per rispondere serve evidenza che ha lei e non hai tu. Se la questione si chiude adesso con una riga, si chiude adesso.

---

## Passo 3 — Selezione delle tecniche

### Criterio di parsimonia

**Regola.** Parti dalla tecnica più semplice che regge il compito e aggiungi una tecnica solo quando sai dire quale modo di fallire corregge. Ogni tecnica aggiunta si porta dietro una riga di motivazione nella consegna: se la riga non ti viene, la tecnica non entra.

**Baseline, valida quasi sempre:** istruzione diretta, contesto sugli obiettivi, formato di output dichiarato. Costa pochi token e risolve la maggior parte dei fallimenti reali, che sono di specificazione e non di ragionamento.

**Ordine di ingresso**, a costo crescente. Sali di gradino solo dopo aver esaurito il precedente.

1. Elementi che non aumentano le chiamate e riducono l'ambiguità: definizione dei termini, criteri di successo, scope, formato e spazio delle risposte.
2. Esemplari, quando formato, tono o confine di un giudizio sono più facili da mostrare che da descrivere.
3. Struttura esplicita del lavoro (piano, sotto-problemi, tabella), quando i passi sono verificabili singolarmente o il piano serve come artefatto.
4. Catena su chiamate separate, quando serve ispezionare o filtrare un risultato intermedio.
5. Ripetizione e voto, solo con metrica e budget dichiarati.

**Tre famiglie del paper sono già interne a Claude 5 e non vanno riprodotte nel prompt.** Il thinking è attivo per default e adattivo, quindi gli induttori di ragionamento aggiungono token senza aggiungere ragionamento; le due eccezioni sono in `modelli.md`. Su Opus 5 l'auto-critica dentro il turno è già comportamento del modello; su Sonnet 5 un'istruzione di verifica resta utile se porta i criteri. Decomposizione e delega sono gestite dal modello nei contesti agentici. Restano utili quando ti serve il ragionamento **come artefatto ispezionabile**: è una ragione diversa dal miglioramento delle prestazioni, e va detta come tale.

### Chi possiede la definizione

`Definizione operativa` e `Scope` dicono cosa mettere nel prompt, non da chi viene: viene da chi ti ha chiesto il prompt. Se la scrivi tu — una soglia, una tassonomia con la sua politica di scarto, il significato operativo di un suo criterio — è la casella 3 o la 5 rimasta vuota, non una tecnica. Torna a chiedere.

### Tassonomia operativa

Le tabelle A-I valgono per ogni destinatario testuale, Claude o altre AI. Sul ramo immagini non si usano: al loro posto ci sono le tecniche per immagini del paper, in `immagini.md`.

In `tecniche.md` restano i numeri che le sostengono, le decisioni di progetto sugli esemplari, le tecniche che restano fuori col perché e le divergenze fra paper e documentazione: si apre quando serve la motivazione quantitativa, quando il compito è multi-stadio, o quando la riga di motivazione non ti viene.

**A. Istruzione, contesto, vincoli** — gradino 1, spesso sufficiente da solo.

| Elemento | Cosa fa | Entra quando | Costo | p. |
|---|---|---|---|---|
| Direttiva | Dichiara l'intento in modo esplicito invece che implicito | Sempre | — | 5 |
| Definizione operativa | Mette nel prompt la definizione del termine chiave invece di darla per nota | Termine specialistico, interno o contestato | Poche righe | 35, 71 |
| Contesto sugli obiettivi | Dice perché serve l'output e come verrà usato | Quasi sempre | Poche righe | 39 |
| Formato di output | Forma, campi, lunghezza, e un esempio se serve | L'output ha una forma attesa | — | 5, 18 |
| Ruolo | Una riga di system prompt che fissa competenza e registro | Task aperti, controllo del tono | — | 12 |
| Istruzioni di stile | Stile, tono, genere dichiarati | Il registro conta | — | 12 |
| Scope | Dice cosa è dentro e cosa fuori dal compito | Compiti stretti, soprattutto su Opus 5 | — | doc |

Il dato che sostiene questa famiglia: togliere dal prompt l'email che spiegava gli scopi del progetto ha fatto crollare l'F1 di 0,27 punti, da 0,45 a 0,18, e il recall di 0,75 (p. 39). Spiegare a cosa serve il lavoro ha reso più degli esemplari.

**B. Esemplari** — gradino 2. Da tre a cinque, dentro `<example>` raccolti in `<examples>`.

| Tecnica | Cosa fa | Entra quando | Costo | p. |
|---|---|---|---|---|
| Few-Shot | Mostra il compito risolto | Formato rigido, giudizio dai confini soggettivi, tono da imitare | Token | 10 |
| Esemplari contrastivi | Aggiunge casi risolti male con il perché sono sbagliati | Esiste un errore ricorrente e riconoscibile | Raddoppia gli esempi | 13, 38 |
| Esemplari ambigui | Include casi di confine con etichetta discutibile | Classificazione in cui il confine è il problema | Token | 32 |
| Esemplari bilanciati | Pareggia le classi rappresentate | Classificazione: la distribuzione degli esempi sposta l'output | — | 32 |
| Esemplari generati (SG-ICL) | Fai produrre gli esempi al modello e correggili | Non hai casi reali sottomano | 1 chiamata | 11 |

Se metti esempi, le sei decisioni di progetto (ordine, quantità, distribuzione, qualità, formato, similarità) sono in `tecniche.md`: l'ordine da solo può spostare l'accuratezza da sotto il 50% a oltre il 90%.

**C. Trattamento dell'input** — quando il problema è la richiesta, non il compito.

| Tecnica | Cosa fa | Entra quando | Costo | p. |
|---|---|---|---|---|
| Rephrase and Respond | Fa riformulare ed espandere la richiesta prima di eseguirla | L'utente finale scrive richieste ellittiche | 0 o 1 chiamata | 12 |
| System 2 Attention | Riscrive l'input eliminando l'irrilevante, poi lavora sul ripulito | Input incollato pieno di rumore | 1 chiamata | 12 |
| Ripetizione del vincolo chiave | Ripete domanda o vincolo in coda al contesto | Contesto lungo, vincolo facile da perdere | Token | 12, 40 |
| Question Clarification | Il modello chiede prima di rispondere se la richiesta è ambigua | Prompt interattivo destinato ad altri utenti | Un turno | 32 |
| Estrazione di citazioni | Prima le citazioni rilevanti in `<quotes>`, poi il compito | Documenti lunghi | Token | doc |

**D. Struttura del lavoro** — entra per rendere il processo ispezionabile, non per far ragionare meglio il modello.

| Tecnica | Cosa fa | Entra quando | Costo | p. |
|---|---|---|---|---|
| Step-Back | Prima una domanda di principio, poi il compito | Vuoi che il criterio scelto sia visibile e discutibile | — | 13 |
| Plan-and-Solve | Piano esplicito, poi esecuzione del piano | Il piano serve come artefatto da approvare | — | 14 |
| Least-to-Most | Elenca i sotto-problemi, poi li risolve in sequenza accumulando | I sotto-risultati sono verificabili uno per uno | n chiamate | 13 |
| DECOMP | Scompone e delega a funzioni o strumenti separati | Esistono strumenti dedicati ai sotto-compiti | n chiamate | 14 |
| Tabella di ragionamento | Emette il ragionamento come tabella | Confronto su dimensioni fisse e ripetute | — | 13 |
| Program-of-Thoughts / PAL | Il ragionamento è codice che viene eseguito | Calcolo, conteggi, aggregazioni su dati | Esecuzione | 14, 24 |
| Skeleton-of-Thought | Scheletro della risposta ed espansione in parallelo | L'obiettivo è la latenza | Chiamate parallele | 14 |
| Tree-of-Thought | Albero di alternative con valutazione e backtracking | Ricerca vera con vincoli; su Claude 5 di solito lo fa già l'agente | Molte chiamate | 14 |

**E. Ripetizione e voto** — solo in pipeline, solo con metrica.

| Tecnica | Cosa fa | Entra quando | Costo | p. |
|---|---|---|---|---|
| Self-Consistency | k risposte a temperatura non nulla, poi maggioranza | Risposta unica e confrontabile, varianza misurata, budget dichiarato | k chiamate | 15 |
| Universal Self-Consistency | L'aggregazione la fa un modello, non un contatore | Output in testo libero, non confrontabile per uguaglianza | k+1 | 15 |
| Meta-CoT | Genera più catene e sintetizza la risposta da tutte | Come sopra | k+1 | 15 |
| Prompt Paraphrasing | Varianti dello stesso prompt | Vuoi misurare quanto il risultato dipende dalla formulazione | k | 15 |

Su Sonnet 5 `temperature` restituisce errore: la varietà si ottiene variando il prompt, non il sampling.

**F. Critica e revisione** — in catena su chiamate separate quando serve leggere o filtrare l'intermedio; dentro il turno solo con i criteri espliciti, e mai su Opus 5.

| Tecnica | Cosa fa | Entra quando | Costo | p. |
|---|---|---|---|---|
| Self-Refine | Bozza, critica, revisione, in chiamate distinte | Vuoi leggere, archiviare o filtrare la critica | 2n chiamate | 15 |
| Chain-of-Verification | Genera domande di verifica sui fatti asseriti e vi risponde prima di revisionare | Output fattuale a rischio di allucinazione, senza strumenti di verifica | 3+ chiamate | 16 |
| Self-Calibration | Seconda chiamata che valuta se la risposta regge | Serve una soglia esplicita per accettare o rigettare | 1 chiamata | 15 |
| CRITIC | Risposta, autocritica, verifica con strumenti esterni | Ci sono strumenti che possono verificare davvero | 3 fasi | 24 |

Una catena su chiamate separate è legittima, perché serve a ispezionare o filtrare un intermedio. Un'istruzione di auto-verifica dentro lo stesso turno rivolta a Opus 5 no, perché duplica un comportamento che il modello ha già. Su Sonnet 5 entra se porta con sé i criteri («verifica il risultato contro i casi limite elencati sopra»); un «ricontrolla» generico non entra su nessun modello.

**G. Agenti e recupero** — pertinente su Claude Code e sulle pipeline con strumenti.

| Tecnica | Cosa fa | Entra quando | Costo | p. |
|---|---|---|---|---|
| ReAct | Ciclo pensiero, azione, osservazione, con tutto in memoria nel prompt | Agente con strumenti in un ambiente | — | 25 |
| Reflexion | Aggiunge alla memoria una riflessione sul fallimento valutato | Il compito si può ritentare e il fallimento è misurabile | — | 25 |
| RAG | Recupera informazione esterna e la inserisce nel prompt | La conoscenza sta fuori dal modello | Retrieval | 25 |
| IRCoT | Alterna recupero e ragionamento, ciascuno guida l'altro | Domande multi-hop | n chiamate | 26 |
| FLARE | Genera una frase provvisoria e la usa come query di ricerca | Generazione lunga e fattuale | Retrieval ripetuto | 26 |

**H. Valutazione, cioè Claude come giudice** — la famiglia con l'evidenza più direttamente applicabile.

| Elemento | Cosa fa | Entra quando | Costo | p. |
|---|---|---|---|---|
| Definizione dei criteri | Mette nel prompt la definizione di ciò che si valuta | Sempre: è l'elemento più presente nei trenta lavori censiti | — | 71 |
| Rubrica generata dal modello | Fai generare i criteri, congelali, poi valuta con quelli | I criteri sono mal definiti e le valutazioni escono incoerenti | 1 chiamata | 27 |
| Output in JSON o XML | Struttura il giudizio | Sempre: la struttura migliora l'accuratezza del giudizio | — | 27 |
| Scala Likert con etichette verbali | Dà significato ai livelli invece di numeri nudi | Punteggi soggettivi | — | 27 |
| Punteggio individuale invece che pairwise | Valuta ciascun testo per sé | Confronti fra testi | — | 28 |
| Un'istanza per chiamata | Evita il batch di più casi in un prompt solo | Quando la qualità conta più del costo | Costo maggiore | 27 |
| Ruoli diversi o dibattito fra ruoli | Diversifica il giudizio | Serve varietà di prospettive, non un verdetto solo | k chiamate | 26 |

**I. Answer engineering** — quando l'output viene letto da codice (pp. 18-19, 76).

| Elemento | Cosa fa | Entra quando | Costo | p. |
|---|---|---|---|---|
| Answer shape | Fissa la forma fisica: token singolo, riga, oggetto | Classificazione, estrazione | — | 18 |
| Answer space | Elenca i valori ammessi | Etichette chiuse | — | 18 |
| Verbalizer | Mappa l'output sull'etichetta interna | Le etichette del sistema non sono quelle naturali per il modello | — | 18 |
| Estrattore | Regex o seconda chiamata che isola la risposta | Pipeline con parser | — | 19 |
| Nomi delle etichette | Sceglie etichette che il modello accetta di produrre | Il modello rifiuta, esita o risponde "non determinabile" | — | 72 |

### Passata di riconoscimento

Prima di consegnare, guarda quello che hai costruito a mano e controlla se ha già un nome in queste tabelle. "Dieci domande numerate da risolvere in sequenza" è `Least-to-Most` (p. 13). "Verifica il risultato contro un criterio esplicito, poi revisiona" è `Chain-of-Verification` (p. 16). "Prima le citazioni rilevanti, poi il compito" è `Estrazione di citazioni`. "Elenca i criteri, congelali, poi giudica" è la rubrica generata della famiglia H.

Se ha un nome, chiamalo col suo nome e con la sua pagina nella riga di motivazione. È quello che rende la motivazione verificabile contro il paper invece di essere una parafrasi, e ti costringe a scegliere dalla tavola intera invece che dalle sette righe della famiglia A.

---

## Passo 4 — Calibrazione e anti-pattern

Prima di consegnare, apri il file del ramo deciso al Passo 1 e applica delta e anti-pattern: `modelli.md` per Claude, `altre-ai.md` per le altre AI, `immagini.md` per i generatori di immagini. La riga delle tecniche del Passo 5 deve nominare il delta che hai applicato e il file da cui viene: se non hai aperto il file non puoi scriverla, ed è così che una lettura saltata si vede nella consegna invece di passare inosservata.

Quando revisioni un prompt esistente, o quando devi mostrare all'utente l'effetto di un anti-pattern rimosso, apri `esempi.md` e riusa la coppia prima/dopo della superficie in questione.

### Regole di scrittura del prompt

Valgono per i prompt testuali. Le due marcate *ramo Claude* vengono dalla documentazione Anthropic; sulle altre AI la struttura si regola con `altre-ai.md`, sulle immagini con `immagini.md`.

- Sii esplicito su formato e vincoli. Se vuoi un comportamento sopra le righe, chiedilo: non va inferito da un prompt vago.
- Spiega il perché di ogni istruzione. "Non usare i puntini di sospensione perché il testo verrà letto da un motore TTS" funziona meglio di "non usare i puntini": il modello generalizza dalla motivazione a casi che non hai previsto, da una regola nuda no.
- *Ramo Claude.* Usa tag XML per separare istruzioni, contesto, esempi e input variabile, con nomi coerenti fra prompt.
- *Ramo Claude.* Contesto oltre i 20k token: documenti in cima, domanda in fondo, ogni documento in `<document>` con `<source>` e `<document_content>`. La domanda in fondo migliora la qualità fino al 30% nei test Anthropic; il paper misura lo stesso ordine di grandezza sul formato della domanda, che sposta l'accuratezza di GPT-3 fino a 30 punti (p. 31).
- Per far agire il modello usa l'imperativo diretto ("modifica questa funzione") e non la forma consultiva ("puoi suggerire modifiche"), che produce suggerimenti invece di azioni.
- Non ripetere nel prompt le preferenze già attive nel profilo dell'utente — lingua, tono, livello terminologico, formato preferito — a meno che il prompt sia destinato a un contesto separato dal suo account, per esempio una chiamata API o una sessione di un'altra persona. Se ci finisce, il prompt le deve portare con sé perché lì non sono attive.

---

## Passo 5 — Consegna

Prima di scrivere la consegna, controlla il prompt finito.

**Conta le domande.** Quante caselle non ha riempito l'utente, e quante domande hai fatto? Se le domande sono meno, la differenza sono caselle decise da te: o diventano domande adesso, anche a prompt scritto, o entrano in ricevuta una per una, con il valore che hai scelto.

**Passa il prompt al setaccio dei numeri.** Rileggi il testo e fermati su ogni numero, data, durata e lunghezza massima: sessanta caratteri per l'oggetto, tre varianti del titolo, ultimi dodici mesi, almeno quattro elementi, sette caratteri di hash. Per ciascuno devi poter dire da quale risposta viene. Quelli che non vengono da una risposta sono decisioni tue anche quando sono prassi del mestiere: o escono dal prompt, o entrano in ricevuta con il valore e il perché. **Un numero piccolo è una soglia come gli altri**: decide cosa viene scartato.

**Cancello di chiusura: scorri le dieci caselle, non la lista delle assunzioni.** Prendi la lista del Passo 2 dalla 1 alla 10 e per ognuna, in ordine, dì chi l'ha riempita: l'utente, un file o la memoria (con la fonte), oppure tu. Ogni casella riempita da te o è già diventata una domanda, o entra in ricevuta. Non c'è una terza via, e l'ovvietà non la apre. Rileggere le assunzioni non basta: una casella riempita e mai scritta lì non c'è, ed è così che superficie e modello, decisi in silenzio, spariscono dalla consegna.

Su ogni casella che finisce in ricevuta chiediti: *se questa è falsa, cambia la struttura del prompt o una parola?* Se cambia la struttura era una domanda: torna al Passo 2 e chiedila, anche a prompt scritto. Se stai per scrivere "se una di queste è falsa il perimetro cambia", l'hai già ammesso. **Anche quando cambia una parola sola**, una soglia o una definizione inventata da te (caselle 3 e 5) è una domanda: la dichiari senza chiedere solo se l'utente ha vietato le domande.

**Le caselle delegate con «Scegli tu» entrano in ricevuta**, con la casella e il valore scelto: «casella 5, delegata: soglia a 900-1200 parole». La delega è dell'utente, la scelta è tua.

**La consegna, in quest'ordine:**

1. **Le assunzioni che restano**, ordinate per impatto, ognuna in una riga a cui l'utente può rispondere sì o no. Se non ce ne sono, una riga: «Assunzioni: nessuna». Vengono prima del prompt perché l'utente le legga prima di copiarlo: dopo, sono una ricevuta e non una domanda.
2. **Il prompt**, in un blocco di codice pronto da copiare, senza commenti interni. Conta le righe di tutti i blocchi: oltre le quaranta circa il prompt si salva come file markdown, e qui va il suo percorso.
3. **Destinatario, modello e superficie**, con quello che il file del ramo dice di dire all'utente fuori dal prompt: un controllo da impostare, un risultato da ricontrollare.
4. **Le tecniche usate**, una riga per ciascuna, col nome di tabella e la pagina quando ce l'hanno, e il fallimento che correggono. Una tecnica senza la sua riga esce dal prompt. Qui va anche il delta di calibrazione, col file del ramo da cui viene.
5. **Le caselle che non sono diventate domande**, raggruppate per chi le ha riempite: le parole dell'utente, oppure memoria, profilo o file del progetto con la fonte. Ogni numero da 1 a 10 compare o qui, o fra le domande, o nelle assunzioni: un numero che non compare in nessuno dei tre è una casella saltata in silenzio.
6. **Su una revisione**, cosa hai rimosso e perché.

Questo passo vale per ogni prompt, anche L1, anche di tre righe: la lunghezza della richiesta non misura niente.

---

## Red flags

Questi pensieri significano che stai per saltare il Passo 2. Sono le frasi ricorrenti osservate nel collaudo della skill, non ipotesi.

| Pensiero | Realtà |
|---|---|
| "La richiesta era già decidibile", "questa casella la so riempire" | Saper decidere non vuol dire che la decisione sia tua. Piena vuol dire riempita dall'utente, da un file, dalla memoria o da una risposta. |
| "Niente intervista: la richiesta era già completa" | Completa sul compito, non sui criteri: questa frase precede un'assunzione strutturale. |
| "Un'assunzione sbagliata si corregge in un secondo giro" | Il secondo giro è una deep research o una sessione su un repo vero. La domanda costa dieci secondi. |
| "Le dichiaro in fondo, così le corregge chi legge" | Le legge dopo aver copiato il prompt. Dichiarare non è chiedere. |
| "La forma dell'output è ovvia, tabella e via" | È la casella 1, la prima della lista: la più assunta in silenzio. |
| "La soglia la propongo io", "scrivo io la definizione operativa" | Soglie e definizioni sono dell'utente. Proporre un valore si fa dentro una domanda; scritto da te è un'assunzione travestita da merito. |
| "Ci siamo, eseguo anche la ricerca", "intanto comincio" | Questa skill consegna un prompt per un'altra sessione. Eseguire il compito è lavoro perso e contesto sporcato. |
| "Il delta del modello lo so a memoria" | I `p. N` e i numeri non sono memorizzabili, e le poche righe che contano stanno nel file del ramo: `modelli.md`, `altre-ai.md` o `immagini.md`. Se non l'hai aperto, non puoi citarlo. |
| "Ha detto SCRIVI SOLO UN PROMPT, quindi non vuole domande" | Ha detto di non eseguire il compito. Sull'intervista non ha detto nulla: le domande si fanno. |
| "Ha vietato le domande, quindi taccio anche sulla contraddizione" | Una domanda passa sempre: quella su una premessa che i fatti smentiscono. Una sola, e dice cosa cambia nel prompt. |
| "Ha vietato le domande, allora salto anche la ricevuta" | È il caso in cui la ricevuta conta di più: assunzioni in testa alla consegna, ordinate per impatto. |
| "Il metodo di ricerca lo imposto io, tanto so come si fa" | È la casella 4. Guarda prima le skill e i file di chi ha lanciato la sessione: se la procedura esiste già il prompt la cita, se non esiste è una domanda. |
| "È un prompt banale", "sono troppe domande, alcune le assumo io" | Nessun tetto: il numero di domande è il numero di caselle vuote, non una tua valutazione. Chi non vuole rispondere ha «Scegli tu». |
| "Ha detto PDF, la casella del formato è piena" | Ha detto la cosa, non il parametro. La lunghezza, il numero di sezioni, il metro di un aggettivo: se l'hai aggiunto tu, quella metà era vuota. |
| "Sessanta caratteri per l'oggetto è prassi, non una decisione" | Nel prompt un numero piccolo taglia come uno grande. Viene da una risposta, oppure è tuo e lo dichiari. |
| "Il dominio lo conosco, i dati del caso li deduco" | Dedurre non è constatare. I dati del caso sono fatti dell'utente: se cambierebbero la struttura della risposta e non li ha scritti, sono la casella 10. |
| "Il criterio che hai scritto tu nella richiesta" | Guarda le sue parole: se il criterio non c'è, è tuo. Attribuirglielo lo rende inattaccabile per chi legge, ed è una decisione tua travestita da fatto. |
| "Qui in chat lo strumento di domanda non c'è", "le domande sono troppe per i bottoni, faccio una lista" | Lo strumento in chat c'è: guarda l'elenco dei tool prima di dire che manca. Il tetto è tre domande per blocco, e i blocchi si incatenano — il numero di domande non decide mai la forma. |
| "L'opzione deve spiegare perché la propongo io" | L'etichetta è il testo di un bottone: due-quattro parole. Il perché sta nella riga sopra il blocco. Un'etichetta lunga è ciò che fa degradare l'intervista in prosa. |

---

*Versione 1.54 — 3 ottobre 2026.*
