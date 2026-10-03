# Tecniche — i numeri, le esclusioni, le divergenze

Allegato di `SKILL.md`. Le tabelle di selezione delle nove famiglie stanno in `SKILL.md`, perché servono a ogni corsa. Qui c'è quello che le sostiene e che serve di rado: i numeri del paper, le decisioni di progetto sugli esemplari, i dettagli operativi per famiglia, le tecniche che restano fuori col perché, e le divergenze fra paper e documentazione Anthropic.

Si apre quando la riga di motivazione di una tecnica non ti viene, quando il compito è multi-stadio, quando serve la cifra per far entrare o restare fuori qualcosa, o quando stai per mettere esemplari in un prompt riusato.

## Perché poche tecniche

Il paper cataloga 58 tecniche testuali in sei famiglie (figura 2.2, p. 9), ma quelle davvero usate sono poche: nel conteggio delle citazioni interne al suo dataset dominano Few-Shot e Chain-of-Thought, con una coda lunghissima (p. 17). Una skill che le elencasse tutte produrrebbe prompt gonfi.

Il motivo è empirico. Nel benchmark del paper su MMLU con gpt-3.5-turbo (p. 33): Zero-Shot 0,627, Zero-Shot-CoT **0,547**, Zero-Shot-CoT con Self-Consistency 0,574, Few-Shot 0,652, Few-Shot-CoT 0,692, Few-Shot-CoT con Self-Consistency 0,691. La complessità non paga in modo monotono: una tecnica su sei ha fatto peggio della baseline, e la Self-Consistency, che triplica le chiamate, non ha migliorato la variante migliore. Gli autori dichiarano i cali inspiegati e definiscono la scelta della tecnica come una ricerca di iperparametri (p. 34). La loro raccomandazione a chi inizia è partire dagli approcci più semplici e restare scettici sulle prestazioni dichiarate (p. 45).

Parsimonia non vuol dire però restare dentro la famiglia A. Vuol dire salire di gradino con una ragione, scegliendo dalla tavola intera: la tecnica giusta chiamata col suo nome costa meno di una sua parafrasi costruita a mano, perché ne conosci il costo e l'evidenza.

---

## Famiglia A — il dato che la sostiene

Il dato più controintuitivo del paper riguarda il contesto sugli obiettivi: nel caso di studio, togliere dal prompt l'email che spiegava gli scopi del progetto ha fatto crollare l'F1 di 0,27 punti, da 0,45 a 0,18, e il recall di 0,75 (p. 39). Spiegare a cosa serve il lavoro ha reso più degli esemplari.

Sulla definizione operativa, il costo di sbagliare è misurato nello stesso caso di studio: interrogato sul costrutto da etichettare, il modello ha dato una definizione diversa da quella dei codificatori umani, e da lì in poi la definizione è stata inclusa in ogni prompt (p. 35). La definizione che conta è quella di chi commissiona il lavoro, non quella che il modello considera ragionevole.

## Famiglia B — le sei decisioni di progetto sugli esemplari

Valgono ogni volta che metti esempi, ed è la parte del paper con l'evidenza numerica più forte (pp. 10-11).

**Ordine**: su alcuni task l'accuratezza varia da sotto il 50% a oltre il 90% al solo variare dell'ordine, quindi in un prompt riusato l'ordine si fissa. **Quantità**: crescente, con benefici che possono saturare oltre i venti esempi, da tre a cinque il punto di equilibrio raccomandato da Anthropic. **Distribuzione delle etichette**: sbilanciata, sbilancia l'output. **Qualità delle etichette**: la letteratura è in contrasto e i modelli grandi tollerano meglio quelle imprecise, il che non è una licenza a metterne di sbagliate. **Formato**: i formati frequenti nei dati di training rendono meglio, e nel caso di studio passare da `Q/R/A` a `Question/Reasoning/Answer` ha alzato la quota di output non parsabili dall'11% al 16% (pp. 40, 74). **Similarità**: esemplari vicini al caso reale in genere aiutano, su alcuni task rende di più la diversità, quindi copri i casi limite.

Nei few-shot le istruzioni generiche battono quelle task-specific su classificazione e QA, e governano attributi ausiliari come lo stile più che la correttezza (p. 11).

## Famiglia C — sulla ripetizione

Il paper offre qui l'aneddoto più onesto della letteratura: nel caso di studio il contesto duplicato per errore ha migliorato le prestazioni, rimuovere il duplicato le ha peggiorate di 0,07 F1, ma triplicarlo non ha aiutato (pp. 40, 42). Ripetere una volta il vincolo che conta è economico e a volte utile, moltiplicarlo no.

`Question Clarification` (p. 32) merita una nota: è la tecnica che fa chiedere al modello prima di rispondere quando la richiesta è ambigua. Entra nei prompt destinati ad altre persone, dove non puoi prevedere quanto sarà ellittica la richiesta in arrivo. Non serve nei prompt che scrivi per l'utente che hai davanti, perché lì l'ambiguità la risolvi al Passo 2 invece di scaricarla a valle.

## Famiglia D — quando il codice batte le parole

Program-of-Thoughts eccelle su matematica e programmazione ed è debole sul ragionamento semantico (p. 14): su un compito numerico, far scrivere ed eseguire codice è più affidabile che far calcolare a parole.

## Famiglia E — due avvertenze

Nel benchmark la Self-Consistency ha migliorato solo la variante zero-shot, non la migliore (p. 33). Nel caso di studio l'ensemble su tre ordinamenti degli esemplari ha peggiorato di 0,16 F1, perché due ordinamenti hanno prodotto output non strutturati e hanno richiesto un estrattore (p. 42): un ensemble cambia anche la forma dell'output, non solo la qualità. Su Sonnet 5 `temperature` restituisce errore, quindi la varietà si ottiene variando il prompt, non il sampling.

## Famiglia F — la distinzione che conta su Claude 5

Una catena su chiamate separate è legittima, perché serve a ispezionare o filtrare un intermedio, ed è il pattern di chaining che la documentazione Anthropic indica come più comune. Un'istruzione di auto-verifica dentro lo stesso turno rivolta a Opus 5 no, perché duplica un comportamento che il modello ha già.

## Famiglia G — sul retrieval iterativo

Le frasi provvisorie generate come piano funzionano meglio, come query, dei titoli dei documenti (p. 26).

## Famiglia H — ordine e ruoli

Nel confronto pairwise conta anche l'ordine degli input, che influenza pesantemente il giudizio (p. 28). E il ruolo serve al registro, poco al giudizio: nei trenta lavori censiti compare in quattro o cinque casi, mentre la definizione del criterio è quasi ovunque (p. 71).

## Famiglia I — tre dettagli operativi

Se l'output contiene ragionamento prima della risposta, conviene che la regex cerchi l'**ultima** occorrenza dell'etichetta, non la prima (p. 19). Un estrattore che recupera output malformati recupera anche risposte sbagliate: nel caso di studio ha alzato l'accuratezza e abbassato l'F1 (p. 41). Le etichette contano: con `accept`/`reject` il modello non collaborava, con `entrapment`/`not entrapment` ha iniziato a rispondere (p. 72).

## Lingua del prompt

Il paper riporta che i template in inglese rendono spesso più di quelli nella lingua del task, ma anche che le traduzioni umane battono quelle automatiche e che nessuna delle due opzioni vince sempre (pp. 20-21). È misurato su modelli pre-2024 e non è confermato dalla documentazione Anthropic. Regola pratica: scrivi il prompt nella lingua dell'output voluto, perché lo stile del prompt influenza lo stile della risposta; usa l'inglese quando il prompt è tecnico e l'output è codice o dati.

---

## Cosa resta fuori, e perché

- **Ottimizzazione automatica dei prompt** (AutoPrompt, APE, GrIPS, ProTeGi, RLPrompt, DP2O, pp. 16-18): richiedono dataset etichettato, metrica e loop automatico. Nominala solo in L3, e in quel caso col dato del paper: DSPy ha battuto il prompt engineer umano sul test set, 0,548 contro 0,53 F1, in sedici iterazioni (p. 42). Se l'utente ha dati e metrica, dirglielo rende più che limare il prompt a mano.
- **Selezione degli esemplari da corpus** (KNN, Vote-K, LENS, UDR, Prompt Mining, Memory-of-Thought, pp. 11, 13): presuppongono una banca di esempi e retrieval a runtime.
- **Ensembling pesante** (DiVeRSe, COSP, USP, MoRE, DENSE, Max Mutual Information, Uncertainty-Routed CoT, Complexity-based, pp. 13-15): molte chiamate, guadagno non dimostrato fuori dai benchmark su cui è stato misurato.
- **Soft prompt, prompt tuning e architetture superate** (pp. 64-65): ottimizzano pesi e non testo, o riguardano i cloze prompt di BERT invece dei prefix prompt di tutti i modelli attuali.
- **Traduzione specialistica** (MAPS, Chain-of-Dictionary, DiPMT, DecoMT, p. 21): compiti fuori scope. Le tecniche per immagini della stessa zona del paper (prompt modifiers, negative prompting, paired-image, image-as-text, p. 22) non sono escluse: stanno in `immagini.md`.
- **Benchmark come previsione**: i numeri su gpt-3.5-turbo, GPT-4, PaLM e LLaMA2 restano ordine di grandezza e direzione, mai stima su Claude 5.

---

## Divergenze fra paper e documentazione Anthropic

**Ragionamento esplicito.** Il paper dedica una famiglia intera agli induttori di ragionamento e usa Zero-Shot-CoT come tecnica di riferimento (pp. 12-13). Su Claude 5 il thinking è attivo per default e adattivo. Vince la documentazione: gli induttori non entrano, e la famiglia resta utile solo quando serve il ragionamento come artefatto visibile. Il conflitto è meno netto di quanto sembri, perché il paper stesso trova Zero-Shot-CoT sotto la baseline zero-shot nel proprio benchmark (p. 33).

**Auto-critica.** Il paper riporta miglioramenti per Self-Refine, Chain-of-Verification e Self-Verification (pp. 15-16); la documentazione di Opus 5 dice di rimuovere le istruzioni di verifica. Non è un compromesso ma una distinzione: la documentazione parla di istruzioni dentro un singolo turno, il paper di catene su chiamate separate. Le catene restano valide quando serve ispezionare l'intermedio; le istruzioni interne restano valide su Sonnet 5 se portano i criteri, e non entrano su Opus 5.

**Output strutturato.** Il paper registra un conflitto irrisolto: strutturare l'output ridurrebbe le prestazioni secondo Tam et al. 2024, le migliorerebbe secondo Kurt 2024, che ne contesta il metodo (pp. 5-6). La documentazione Anthropic è netta: structured outputs esiste come funzione e i modelli recenti rispettano schemi complessi quando glielo chiedi. Sui prompt di valutazione le due fonti concordano, perché anche il paper misura che JSON o XML migliorano l'accuratezza del giudizio (p. 27). Vince la documentazione.

**Ruolo e persona.** Il paper è tiepido: il role prompting migliora gli output aperti e "in some cases" l'accuratezza sui benchmark (p. 12), e nella tabella dei valutatori i ruoli compaiono in pochi lavori su trenta (p. 71). La documentazione raccomanda un ruolo nel system prompt anche di una sola frase. Non è una vera contraddizione: il ruolo governa registro e focus, non correttezza. Lo tengo, con quella motivazione.

## Fonti

Sander Schulhoff et al., *The Prompt Report: A Systematic Survey of Prompt Engineering Techniques*, arXiv:2406.06608v6, 26 febbraio 2025. Copre la letteratura fino a febbraio 2024, 1.565 paper selezionati con processo PRISMA, tassonomia testuale in figura 2.2, p. 9.

Documentazione Anthropic, consultata il 9 settembre 2026:

- `https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices`
- `https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-opus-5`
- `https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/prompting-claude-sonnet-5`
