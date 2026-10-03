# Esempi — prima e dopo

Allegato di `SKILL.md`. Quattro coppie, una per superficie, costruite sugli anti-pattern elencati in `modelli.md`. Servono in revisione: quando togli un anti-pattern, la coppia della superficie in questione mostra all'utente cosa cambia senza doverlo spiegare a parole. Non sono modelli da ricopiare: cambia il compito, resta la trasformazione.

---

## 1. Chat su claude.ai

Anti-pattern: induttori di ragionamento, imperativo enfatico e maiuscole di urgenza, istruzioni in negativo, opinioni personali nel prompt.

**Prima**

```
Sei un esperto di comunicazione. Pensa passo passo prima di rispondere.
Analizza il testo qui sotto. È FONDAMENTALE che tu NON usi elenchi puntati,
NON usi markdown e NON sia generico.
A me sembra scritto male, sei d'accordo?

[testo incollato]
```

**Dopo**

```
<compito>
Stabilisci se il testo in <testo> funziona come apertura di una newsletter
tecnica letta da chimici analitici, e motiva il giudizio.
</compito>

<criteri>
Il lettore capisce in due righe di cosa si parla; i termini specialistici
sono usati con il loro significato preciso; non promette contenuti che il
resto della newsletter non mantiene.
</criteri>

<formato>
Prosa continua, tre paragrafi al massimo, nessun elenco: il testo finisce
dentro una mail e gli elenchi la spezzano.
</formato>

<testo>
[testo incollato]
</testo>
```

**Cosa è cambiato.** L'induttore sparisce, il thinking è già attivo. Le maiuscole sparivano comunque senza effetto, e al loro posto c'è il registro piano. I tre divieti diventano una prescrizione positiva con il perché, così il modello generalizza a casi non previsti. La domanda "sei d'accordo?" anticipava il giudizio e lo spostava per compiacenza: la richiesta è neutra e i criteri sono espliciti.

---

## 2. Claude Code

Anti-pattern: default ciechi, istruzioni di auto-verifica rivolte a Opus 5, scaffolding di progresso forzato, imperativo enfatico.

**Prima**

```
Sistema il bug nel parser CSV. DEVI SEMPRE ricontrollare il tuo lavoro
prima di dire che hai finito. Se hai dubbi, usa i subagent.
Ogni tre tool call scrivi un riassunto di cosa hai fatto.
Non rompere niente.
```

**Dopo**

```
In src/import/csv.py le righe con il separatore dentro le virgolette
finiscono nella colonna sbagliata. Correggi il parsing e allinea i test.

Scope: quel file e i suoi test. Niente refactor, niente feature oltre la
correzione, niente gestione errori per scenari che non si verificano.
La soluzione deve valere su tutti gli input validi, non solo sui casi di
test: se un test è sbagliato, dillo invece di aggirarlo.
Delega a un subagent solo se serve leggere un modulo grosso e indipendente.
Se crei file di lavoro per iterare, ripuliscili prima di chiudere.
```

**Cosa è cambiato.** Il "ricontrolla sempre" produce over-verification su Opus 5, che verifica già: al suo posto c'è il criterio vero, cioè cosa deve reggere la correzione. "Se hai dubbi, usa i subagent" era un default cieco e diventa una condizione mirata. Il riassunto ogni tre tool call degradava gli aggiornamenti che il modello dà già bene. Le maiuscole lasciano il posto a uno scope dichiarato, che è il vincolo che serviva davvero.

---

## 3. System prompt riutilizzabile

Anti-pattern: presupposizioni false nell'istruzione, chiedere al modello di spiegare il proprio ragionamento, fidarsi di un punteggio di confidenza verbalizzato, etichette che il modello non collabora a produrre.

**Prima**

```
Sei un assistente esperto di compliance.
Individua tutti i punti in cui la richiesta del cliente viola la policy.
Spiega il tuo ragionamento passo dopo passo.
Dai un punteggio di confidenza da 0 a 100.
Se non sei sicuro rispondi "non determinabile".
```

**Dopo**

```
<ruolo>
Valuti richieste di clienti contro la policy interna di una società di
servizi. Registro asciutto, nessuna consulenza legale.
</ruolo>

<compito>
Stabilisci se la richiesta in <richiesta> è conforme alla policy in
<policy>. Riporta i criteri applicati e il passaggio di testo su cui ti
basi, non il processo con cui ci sei arrivato.
</compito>

<policy>{{POLICY}}</policy>
<richiesta>{{RICHIESTA}}</richiesta>

<formato>
Un oggetto JSON, nient'altro:
{"esito": "conforme" | "non_conforme" | "policy_non_copre_il_caso",
 "criterio": "<clausola della policy>",
 "evidenza": "<citazione dalla richiesta>"}
esito ammette solo quei tre valori.
</formato>
```

**Cosa è cambiato.** "Individua tutti i punti in cui viola" presupponeva la violazione e la compiacenza faceva il resto: la formulazione è neutra e ammette l'esito negativo. Il ragionamento introspettivo diventa criteri ed evidenze, che sono verificabili. Il punteggio di confidenza esce dall'output, perché resta sovraconfidente anche con ragionamento esplicito e non è una probabilità; se serve una soglia, si fa in una seconda chiamata di Self-Calibration. "Non determinabile" era l'etichetta in cui il modello si rifugiava: sostituita da un'etichetta che dice una cosa precisa e che accetta di produrre. I placeholder stanno in tag propri, con nomi stabili fra le istanze.

---

## 4. Chiamate API

Anti-pattern: prefill della risposta, istruzioni in negativo, induttori di ragionamento, parametri non più accettati.

**Prima**

```python
messages=[
  {"role": "user", "content":
     "Classifica il ticket. Pensa passo passo. Non aggiungere preamboli, "
     "non spiegare, non usare markdown. Rispondi SOLO con l'etichetta.\n\n" + ticket},
  {"role": "assistant", "content": "Etichetta:"},
]
temperature=0.2
```

**Dopo**

```python
system=(
  "Classifichi ticket di assistenza per instradarli. "
  "L'output viene letto da un parser, non da una persona."
)
messages=[
  {"role": "user", "content":
     "<ticket>\n" + ticket + "\n</ticket>\n\n"
     "Restituisci l'etichetta dentro <etichetta></etichetta>.\n"
     "Valori ammessi: fatturazione, tecnico, commerciale, altro."},
]
```

**Cosa è cambiato.** Il prefill dell'assistant restituisce 400 dai modelli 4.6 in poi: il preambolo si toglie chiedendo l'output dentro un tag, oppure con structured outputs se il parser è a valle di un contratto stabile. `temperature` su Sonnet 5 restituisce errore, e la sua rimozione non toglie determinismo al risultato, perché la varietà si governava dal prompt comunque. L'induttore spariva insieme al thinking di default. I tre divieti diventano una forma dichiarata più uno spazio delle risposte chiuso, che è quello che il parser voleva. L'estrattore cerchi l'ultima occorrenza del tag, non la prima.
