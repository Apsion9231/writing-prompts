# Altre AI — calibrazione per i modelli testuali non Claude

Allegato di `SKILL.md`. Si apre prima di consegnare un prompt destinato a ChatGPT, Codex, Gemini o a qualunque modello testuale che non sia Claude, e su ogni revisione di un prompt scritto per loro. Per i prompt che generano o modificano immagini si apre invece `immagini.md`.

Questo strato è volutamente sottile. La selezione delle tecniche, l'intervista e i cancelli di `SKILL.md` valgono qui come su Claude: cambia solo quello che segue. Non ci sono casi per fornitore, e non ci sono nomi di versione, perché invecchiano più in fretta di questo testo. Se una scelta di calibrazione è decisiva per il risultato e dipende da come si comporta oggi un modello preciso, verifica la guida corrente del fornitore invece di applicare per analogia queste righe o i delta di `modelli.md`.

## Struttura proporzionata

I fornitori non sono d'accordo sulla struttura. Una guida raccomanda di partire dal risultato voluto e di tenere il prompt breve; un'altra raccomanda delimitatori coerenti, tag in stile XML o heading Markdown, con le istruzioni critiche in cima. La regola che regge su entrambi è quella di parsimonia applicata alla forma:

- **Delimitatori solo dove separano qualcosa.** Il materiale incollato e l'input variabile stanno in un blocco delimitato, perché è l'unico confine fra quello che il modello deve eseguire e quello che deve leggere. Sezioni etichettate, ruoli in un tag, istruzioni divise in cinque blocchi: no.
- **Un solo sistema di delimitazione per prompt**, tag oppure heading, mai mescolati.
- **Parti dal risultato.** Descrivi cosa deve arrivare e a cosa serve; descrivi i passaggi solo quando il processo stesso è un vincolo.
- **Materiale lungo prima, richiesta in fondo**, con una frase che li collega ("sulla base del testo sopra…"). Su questo le guide concordano.

## Superficie

Il default è la chat web o l'app: niente parametri, niente sampling, niente livelli di ragionamento da impostare. Citarli nel prompt è rumore. Se il prompt va su un'API, dillo nella consegna e segnala che i parametri si regolano fuori dal testo.

Le preferenze trasversali dell'utente (lingua, tono, formato abituale) su questi prodotti stanno nelle impostazioni di personalizzazione dell'account, non nel prompt: nel prompt resta ciò che è specifico del compito.

## Cosa va chiesto a parole

- **Lunghezza e articolazione.** Alcuni modelli rispondono in modo sintetico per default: se servono motivazioni, alternative o un testo lungo, va scritto.
- **Grounding, in una delle due direzioni.** Se la risposta dipende da fatti correnti: cerca e cita le fonti. Se il compito è lavorare su materiale fornito: attieniti al materiale e dichiara quando un'informazione non c'è. Un prompt che non sceglie lascia al modello la scelta fra inventare e cercare.
- **Modelli piccoli e piani gratuiti.** Le guide sono scritte per i modelli pieni. Con un modello più piccolo, o quando non sai quale risponderà, pesa di più l'esplicito: formato, criteri di accettazione, cosa fare se manca un dato.

## Verifica incrociata

Uso ricorrente: far controllare a un'altra AI un lavoro fatto altrove.

- **Chiedi errori, non conferme.** "Verifica che sia corretto" induce accordo; "elenca gli errori fattuali, i passaggi non supportati dal materiale e le affermazioni che non riesci a verificare" no. È la stessa compiacenza delle presupposizioni false in `modelli.md` (p. 32), vista dal lato opposto.
- **Presenta il testo in modo neutro.** Non dire che l'ha scritto un'altra AI né che l'ha scritto l'utente: le due attribuzioni spostano il giudizio in direzioni opposte.
- **Dai al verificatore il criterio**, non solo il testo: rispetto a cosa è sbagliato (l'articolo fornito, una norma, un dataset).

## Anti-pattern da togliere

- **Stile Claude trasportato.** Tag XML decorativi, sezioni multiple, ruolo elaborato, istruzioni di verifica ridondanti. È il difetto più probabile di un prompt scritto con questa skill, perché il resto della skill è ottimizzato su Claude.
- **Induttori di ragionamento** ("pensa passo passo", "ragiona prima di rispondere"): i modelli attuali dei principali fornitori ragionano già internamente. Sui problemi davvero difficili basta una riga che chieda di riflettere a fondo.
- **Enfasi e maiuscole di urgenza** ("IMPORTANTE", "OBBLIGATORIO", "NON… MAI"): il registro piano ottiene lo stesso effetto. Il caso di studio del paper lo misura: la maiuscola non ha cambiato il comportamento (p. 73).
- **Istruzioni solo in negativo**: "non inventare" diventa "se un'informazione non è nel testo, scrivi che manca".
- **Tutte le fonti allegate per sicurezza**: solo il materiale che può cambiare la risposta, dicendo cosa prendere da ciascun pezzo.
- **Dieci vincoli**: uno o due, quelli che se violati rendono inutilizzabile il risultato.

---

## Fonti

Consultate il 13 settembre 2026:

- `https://learn.chatgpt.com/docs/prompting` — guida OpenAI al prompting per ChatGPT e Codex: partire dal risultato, prompt brevi.
- `https://developers.openai.com/api/docs/guides/prompt-guidance` — guida OpenAI al modello corrente via API.
- `https://ai.google.dev/gemini-api/docs/prompting-strategies` — strategie di prompting Google, aggiornata il 10 giugno 2026: delimitatori coerenti, istruzioni critiche in cima, contesto lungo prima e domanda in fondo.

La verifica incrociata, il tier gratuito e gli anti-pattern vengono da una versione precedente di questa skill (fonti consultate il 31 agosto 2026), rese generiche. Le guide dei fornitori cambiano a ogni release e negli ultimi due anni hanno cambiato anche direzione: riverificale prima di contare su un dettaglio.
