# Superfici — approfondimento

Allegato di `SKILL.md`. La tabella del Passo 1 basta a classificare la superficie; questo file serve a scrivere il prompt una volta classificata. Apri solo la sezione della superficie di destinazione.

## Chat su claude.ai

Solo leve testuali. Niente effort, thinking config o parametri di sampling: citarli è rumore. La separazione fra istruzioni e input si rende con tag XML dentro un blocco unico.

Le leve disponibili sono quelle della famiglia A in `SKILL.md` — direttiva, definizione operativa, contesto sugli obiettivi, formato di output, ruolo, istruzioni di stile, scope — più gli esemplari e il trattamento dell'input (famiglie B e C in `SKILL.md`) quando servono. Tutto il resto della tassonomia presuppone più chiamate: in chat una catena si fa a turni successivi della stessa conversazione, quindi è un piano di lavoro da dichiarare, non una tecnica interna al singolo prompt.

Non essendoci sampling da regolare, la varietà fra due risposte si ottiene variando il prompt e non i parametri.

## Claude Code

Da valutare uno per uno: ogni blocco incluso senza motivo è un vincolo in più da leggere.

- **Contro l'over-engineering**: niente feature, refactor o miglioramenti oltre il richiesto; niente docstring o commenti su codice non toccato; niente gestione errori per scenari impossibili; niente astrazioni per operazioni una tantum.
- **Contro l'hardcoding**: soluzione generale valida su tutti gli input validi, non solo sui test; i test verificano la correttezza, non definiscono la soluzione; se il compito è infattibile o un test è sbagliato, dirlo invece di aggirarlo.
- **Contro le allucinazioni sul codice**: nessuna affermazione su file non aperti, leggere i file rilevanti prima di rispondere.
- **Azioni distruttive**: azioni locali e reversibili libere; conferma prima di operazioni difficili da annullare o visibili ad altri, cioè force push, reset hard, cancellazioni, push, commenti su PR. Mai usare scorciatoie distruttive per aggirare un ostacolo.
- **File temporanei**: se ne crea per iterare, ripulirli a fine task.
- **Subagent**, soprattutto su Opus 5: delegare solo per lavori grossi e genuinamente indipendenti; non delegare quel che si chiude in pochi tool call; non usare subagent per verificare il proprio lavoro; tenere basso il numero di spawn.
- **Chiamate parallele**: tool call indipendenti in parallelo, sequenziali solo quando una dipende dal risultato della precedente.
- **Task lungo su più context window**: dire al modello che il contesto verrà compattato e che non deve chiudere il lavoro in anticipo per timore di esaurire il budget, salvando invece lo stato prima del refresh.
- **Pacchetti inesistenti**: il codice generato può importare pacchetti che non esistono, e quel nome può essere registrato da un attaccante con dentro codice malevolo (p. 30). Nei prompt che installano dipendenze, chiedi di verificare che il pacchetto esista e sia quello atteso.

## System prompt riutilizzabile

Istanziato su input diversi: servono placeholder espliciti, formato vincolato e, se l'output lo legge del codice, answer engineering (famiglia I in `SKILL.md`).

Come si struttura.

- **Placeholder.** L'input variabile sta in un tag XML suo, con un nome che dice cosa contiene e resta uguale in tutte le istanze del prompt. La regola sui tag XML in `SKILL.md` vale qui più che altrove, perché è l'unico confine fra le istruzioni fisse e il contenuto che cambia a ogni chiamata.
- **Ruolo.** Una riga che fissa competenza e registro, in testa al system prompt.
- **Formato vincolato.** Forma, campi e lunghezza dichiarati una volta sola nel prompt, non ricontrattati a ogni istanza.
- **Esemplari.** Se ne metti, l'ordine si fissa: è un prompt riusato, e su alcuni task l'accuratezza varia da sotto il 50% a oltre il 90% al solo variare dell'ordine (famiglia B in `SKILL.md`, decisioni di progetto in `tecniche.md`).
- **Promemoria di coda.** Su Opus 5, in un system prompt lungo, l'istruzione principale va affiancata da un promemoria verso la fine; la forma è in `modelli.md`.

Un system prompt è L2 per definizione, quindi l'attrito di una domanda di intervista si ammortizza su molte esecuzioni: qui l'intervista del Passo 2 conviene quasi sempre.

## Chiamate API

Qui esistono `effort`, `thinking`, `max_tokens`, structured outputs. Il blocco parametri entra solo qui.

I valori concreti stanno in `modelli.md`, riga «Solo API» del delta di Sonnet 5: effort di default, parametri di sampling che restituiscono 400, extended thinking manuale, e il tokenizer nuovo che gonfia i conteggi e può far troncare i `max_tokens` ereditati da un progetto vecchio. Non ricopiarli qui: se cambiano, cambiano in un posto solo.

Due conseguenze pratiche sul testo del prompt.

- **Niente prefill.** Dai modelli 4.6 in poi restituisce errore 400. Per eliminare i preamboli, chiedilo come istruzione, fai produrre l'output dentro tag XML, o usa structured outputs.
- **Output consumato da codice.** Vale l'answer engineering della famiglia I in `SKILL.md`: forma della risposta, spazio delle risposte, verbalizer, estrattore, nomi delle etichette. Su structured outputs la documentazione Anthropic vince sul paper, e la divergenza è motivata in fondo a `tecniche.md`.

- Se il prompt vivrà a lungo su un'API, ricorda che il modello dietro cambia nel tempo e con esso il risultato (prompt drift, p. 31): una riga di commento con data e modello di taratura si ripaga.
