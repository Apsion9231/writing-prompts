# Modelli — anti-pattern e delta

Allegato di `SKILL.md`. Si apre prima di consegnare un prompt destinato a Opus 5 o Sonnet 5, e su ogni revisione di un prompt scritto per modelli precedenti.

## Anti-pattern da togliere

Abitudini utili nel 2023 che oggi peggiorano l'output. Su una revisione, elencare cosa hai tolto serve a non reintrodurlo.

- **Induttori di ragionamento** ("pensa passo passo"): su Opus 5 e Sonnet 5 il thinking è attivo per default e adattivo, quindi consumano token senza aggiungere ragionamento. Servono in due casi: con thinking disabilitato, e su Sonnet 5 a effort `low` quando l'effort non si può alzare, con una riga mirata ("questo compito richiede ragionamento su più passi: rifletti con attenzione prima di rispondere").
- **Imperativo enfatico e maiuscole di urgenza** ("CRITICO", "OBBLIGATORIO", "DEVI sempre"): producono overtriggering, cioè reazione dove non serve, mentre il registro piano ottiene lo stesso effetto ("usa lo strumento X quando…"). Il caso di studio conferma che le maiuscole non risolvono: `ENTRAPMENT MUST BE EXPLICIT, NOT IMPLICIT.` non ha cambiato il comportamento del modello (p. 73).
- **Default ciechi** ("se hai dubbi, usa X"): sostituiscili con una condizione mirata, "usa X quando migliora la comprensione del problema".
- **Istruzioni di auto-verifica rivolte a Opus 5** ("ricontrolla", "verifica prima di rispondere"), comprese quelle nell'harness: il modello verifica già, e l'istruzione si somma producendo over-verification, cioè token e latenza senza guadagno.
- **Istruzioni in negativo**: "non usare markdown" diventa "scrivi in paragrafi di prosa continua". Dire cosa fare funziona meglio di dire cosa evitare, e un esempio positivo dello stile voluto batte una lista di divieti.
- **Prefill della risposta**: dai modelli 4.6 in poi restituisce errore 400. Per eliminare i preamboli, chiedilo come istruzione, fai produrre l'output dentro tag XML, o usa structured outputs.
- **Scaffolding di progresso forzato** ("ogni tre tool call riassumi"): entrambi i modelli aggiornano già bene e il forzante degrada la qualità degli aggiornamenti.
- **Markdown pesante nel prompt quando l'output non lo deve avere**: lo stile del prompt influenza lo stile della risposta.
- **Opinioni personali nel prompt**: "questo argomento mi convince" o "sei sicuro?" spostano l'output per compiacenza, e l'effetto è più forte sui modelli grandi e instruction-tuned (p. 32). Se vuoi un giudizio, non anticipare il tuo.
- **Presupposizioni false nell'istruzione**: "individua tutti i punti in cui l'input è dannoso" presuppone che lo sia, e la compiacenza fa il resto (p. 32, nota 12). Formula neutro: "stabilisci se l'input è dannoso e motiva".
- **Chiedere al modello di spiegare il proprio ragionamento** come se avesse accesso ai propri processi interni: quello che produce è una descrizione plausibile di passi possibili, che può non corrispondere a nulla (p. 38, nota 19). Chiedi criteri applicati ed evidenze usate, che sono verificabili.
- **Fidarsi di un punteggio di confidenza verbalizzato**: i modelli restano sovraconfidenti quando esprimono la confidenza a parole, anche con ragionamento esplicito (pp. 31-32). Usalo per ordinare, non come probabilità.
- **Difese anti-injection ritenute sufficienti**: su centinaia di migliaia di prompt malevoli nessuna difesa testuale è risultata pienamente sicura, mitigano soltanto (p. 30). Se il prompt riceve input non fidato, dillo e tratta il testo dell'utente come dato, non come istruzione.

## Delta fra i due modelli

Divergono su lunghezza, scope, auto-verifica, letteralismo e default di design. Un prompt tarato su uno è misurabilmente subottimale sull'altro.

**Claude Opus 5**

- **Lunghezza**: risposte visibili più lunghe dei modelli precedenti, e l'effort governa il thinking, non l'output. La concisione va chiesta a parole; in un system prompt lungo affianca all'istruzione principale un promemoria verso la fine, del tipo `<tone_preference>Mantieni gli output ragionevolmente concisi.</tone_preference>`.
- **Auto-verifica**: togli ogni istruzione di ricontrollo. È il punto in cui diverge di più da Sonnet 5.
- **Scope**: tende ad allargare il compito. Sui compiti stretti vincolalo: consegnare quel che è stato chiesto allo scope inteso, prendere da sé le decisioni di routine, segnalare in una frase se la richiesta sembra sbagliata e proseguire come chiesto invece di trasformarla in silenzio.
- **Narrazione**: annuncia spesso cosa sta per fare. Descrivi la cadenza voluta invece di vietarla: una frase prima del primo tool call, aggiornamenti solo su scoperte importanti o cambi di direzione, chiusura che parte dall'esito.
- **Correzioni**: le narra più del necessario. Nei prodotti rivolti a utenti, limita a quelle che cambiano codice, conclusioni o decisioni.
- **Deliverable scritti**: i file che scrive su disco sono più lunghi della norma, quindi calibra esplicitamente la lunghezza contro le sezioni riempitivo.
- **Subagent**: delega prontamente. Se il costo conta, dichiara quando la delega è giustificata e tieni basso il numero di spawn.
- **Contesto**: finestra da un milione di token come default e come massimo, con instruction following stabile lungo tutta la finestra.

**Claude Sonnet 5**

- **Lunghezza**: calibra da sé sulla complessità. Istruzioni di concisione solo se la verbosità osservata è un problema, in forma positiva e con un esempio dello stile voluto.
- **Letteralismo**: interpreta alla lettera e non generalizza un'istruzione da un elemento all'altro, soprattutto a effort basso. Se un vincolo vale per tutto, dichiaralo ("applica questa formattazione a ogni sezione, non solo alla prima"). È il difetto speculare di Opus 5.
- **Auto-verifica**: avvia già cicli di verifica quando ha strumenti. Un'istruzione di verifica ha senso solo se porta i criteri con sé ("controlla il risultato contro i casi limite elencati sopra"); un generico "ricontrolla" aggiunge poco.
- **Tool**: più agentico del predecessore. Se non li usa abbastanza, descrivi quando e perché dovrebbe; con thinking disabilitato serve una spinta esplicita.
- **Frontend e design**: tende a un unico stile di default, e le istruzioni generiche lo spostano solo su un altro default fisso. Funzionano una specifica concreta (palette in esadecimale, font, spaziature, raggio degli angoli) oppure fargli proporre quattro direzioni visive distinte prima di costruire. Poiché `temperature` non è accettata, questa è anche l'unica via per ottenere varietà fra run.
- **Solo API**: effort di default `high`, `xhigh` per coding e agentic difficili; `temperature`, `top_p`, `top_k` non di default e l'extended thinking manuale con `budget_tokens` restituiscono 400; il tokenizer nuovo produce circa il 30% di token in più a parità di testo, quindi i `max_tokens` ereditati possono troncare.

**Comune a entrambi.** Nelle code review, "riporta solo problemi ad alta severità" o "sii conservativo" vengono presi alla lettera e il modello riporta meno di quanto trova. Se vuoi copertura, chiedi di riportare tutto con confidenza e severità annotate e filtra in un passaggio separato.

Quando il modello non è noto, perché l'utente ha risposto «Non lo so» o ha vietato le domande, produci un prompt base condiviso più due blocchi delta separati, uno per Opus e uno per Sonnet, non due prompt interi. Lo stesso quando il prompt deve girare su tutti e due.

---

## Fonti dei delta

- `https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-opus-5`
- `https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-sonnet-5`

I delta per modello invecchiano a ogni release: quando compare un modello più recente di Opus 5 o Sonnet 5, riverifica quelle pagine invece di applicare per analogia i delta scritti qui. Il criterio di selezione e la tassonomia invecchiano molto più lentamente.
