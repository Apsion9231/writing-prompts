# Immagini — prompt per generatori e per l'editing

Allegato di `SKILL.md`. Si apre quando il prompt deve generare o modificare un'immagine, con qualunque generatore: dentro una chat (ChatGPT, Gemini) o in uno strumento dedicato. Le tabelle A-I di `SKILL.md` qui non si usano, perché sono pensate per modelli testuali; restano valide l'intervista, il criterio di parsimonia e la consegna.

Niente nomi di versione e niente casi per fornitore. Dove i generatori si comportano in modo diverso, la regola è scritta come condizione su quello che il generatore offre.

## Le dieci caselle, per un'immagine

L'intervista del Passo 2 non cambia: stesse caselle, stesso ordine, una domanda per casella vuota. Cambia cosa significano.

| Casella | Su un'immagine |
|---|---|
| 1. Forma dell'artefatto | Proporzioni, orientamento, risoluzione, quante varianti; dove finisce l'immagine (copertina, stampa, post, sfondo) |
| 2. Perimetro | Cosa è in scena e cosa no; generazione da zero o modifica di un'immagine esistente |
| 3. Definizioni di dominio | Lo stile nominato a parole dall'utente ("editoriale", "pulito", "vintage"): cosa vuol dire in termini visivi |
| 4. Metodo e strumenti | Quale generatore, e quali controlli nativi offre: proporzioni, campo negativo, maschera, immagini di riferimento |
| 5. Soglie e numeri | Quanto testo nell'immagine, quanti soggetti, margini per un titolo sovrapposto |
| 6. Campi e contenuto | Soggetto, azione, luogo, composizione e inquadratura, luce, ottica, materiali, palette |
| 7. Destinatario | Il generatore; se non è deducibile, è la prima domanda |
| 8. Consumatore | Chi guarda e dove: sito, stampa, social, uso commerciale |
| 9. Vuoto e premessa falsa | In un editing: cosa deve restare identico. In generale: cosa fare se un vincolo non è realizzabile (testo troppo lungo, riferimento incoerente) |
| 10. Dati del caso | Il soggetto reale che l'immagine deve riprodurre (il prodotto, il luogo, la persona, il marchio veri) e se l'utente ne ha una foto |

## Tecniche del paper per le immagini

Il paper dedica alle immagini una sezione breve (§ 3.2.1, p. 22). Sono l'equivalente, per questo ramo, delle tabelle A-I: chiamale col loro nome nella riga delle tecniche.

| Tecnica | Cosa fa | Entra quando | p. |
|---|---|---|---|
| Prompt Modifiers | Parole che cambiano la resa: mezzo ("olio su tela"), luce, ottica, stile | Quasi sempre: sono il lessico della casella 6 | 22 |
| Negative Prompting | Pesa in negativo termini da evitare | Solo se il generatore ha un campo o una sintassi negativa; altrimenti vedi le esclusioni sotto | 22 |
| Paired-Image Prompting | Mostra una coppia prima/dopo e chiede la stessa trasformazione su un'immagine nuova | Editing ripetuto con lo stesso effetto su più immagini, con un generatore che accetta immagini in ingresso | 22 |
| Image-as-Text | Descrive a parole un'immagine di riferimento e usa la descrizione nel prompt | Il generatore non accetta riferimenti, o serve fissare cosa del riferimento conta | 22 |

## Principi su cui le guide convergono

1. **Concreto batte generico.** Soggetto, materiali, colore, luce e composizione nominati. "Bello" e "professionale" non portano informazione visiva.
2. **Frasi, non liste di keyword.** Un paragrafo descrittivo coerente, lungo quanto serve a fissare gli elementi che contano; allungare solo per aggiungere vincoli che cambiano davvero l'immagine.
3. **Ordine stabile**: soggetto e azione, poi luogo e contesto, poi composizione e stile, infine i vincoli. Apri con il verbo che dichiara l'operazione: genera, modifica, sostituisci.
4. **Lessico fotografico e cinematografico**: inquadratura, angolo di ripresa, lunghezza focale, diaframma e profondità di campo, schema di luce, temperatura colore, grana.
5. **Descrivi ciò che vuoi.** "Strada deserta" invece di "senza auto": nominare l'oggetto da escludere tende a farlo comparire.
6. **Testo nell'immagine fra virgolette**, il più breve possibile, con carattere, colore e posizione descritti. L'ortografia la controlla l'utente sul risultato: diglielo nella consegna, fuori dal prompt, perché chiederlo al generatore non gliela fa rileggere.
7. **Si parte semplici e si cambia una cosa alla volta.** I follow-up brevi ("tieni tutto, scalda la luce") sono il meccanismo previsto, non un ripiego.

## Editing e riferimenti

- **Dì cosa cambia e cosa resta identico**: identità dei soggetti, luce, prospettiva, composizione, stile. "Cambia solo l'insegna; tutto il resto dell'immagine resta esattamente com'è." Ripeti il vincolo di conservazione a ogni giro, perché ogni giro può spostarlo.
- **Più immagini di riferimento: un ruolo per ciascuna**, nominandole o numerandole ("dalla prima prendi il volto, dalla seconda la luce").
- **Anche con una maschera**, scrivi nel testo cosa cambia dentro la zona e cosa si preserva: su diversi generatori la maschera è un'indicazione, non un confine esatto.

## Dove i generatori divergono: le condizioni

| Aspetto | Se il generatore… | Allora |
|---|---|---|
| Esclusioni | ha un campo negativo o una sintassi di peso | Prima riscrivi la scena in positivo; se resta un elemento indesiderato, mettilo nel campo come sostantivo nudo, senza "no" |
| | non ce l'ha | Esclusione scritta in positivo dentro la frase; una frase breve di esclusione solo se la forma positiva non esiste |
| Proporzioni | ha un controllo nativo (parametro, menu) | Dillo all'utente nella consegna, fuori dal prompt, e non ripeterle nel testo in forma diversa |
| | non ce l'ha | Proporzioni e orientamento scritti a parole nel prompt |
| Riscrittura automatica del prompt | la applica | Se serve controllo preciso, segnala all'utente di disattivarla o di rileggere la versione riscritta |
| Testo nell'immagine | lo rende male sulle scritte lunghe | Testo al minimo; le composizioni tipografiche complesse si fanno fuori dal generatore |

Se il generatore non è noto, scrivi la versione in positivo con le proporzioni a parole: regge ovunque.

## Fuori dal prompt, per l'utente

Molti generatori marcano le immagini prodotte con watermark invisibili o credenziali di contenuto. Se l'immagine è destinata a un sito, a materiale di vendita o a uso commerciale, dillo nella consegna.

---

## Fonti

Paper: Schulhoff et al., *The Prompt Report*, § 3.2.1, p. 22.

Guide ufficiali, consultate il 13 settembre 2026:

- `https://developers.openai.com/cookbook/examples/multimodal/image-gen-models-prompting-guide` — guida OpenAI al prompting per la generazione di immagini (aggiornata il 21 aprile 2026)
- `https://developers.openai.com/api/docs/guides/image-prompting`
- `https://developers.openai.com/api/docs/guides/image-generation`
- `https://ai.google.dev/gemini-api/docs/image-generation`
- `https://docs.cloud.google.com/vertex-ai/generative-ai/docs/image/img-gen-prompt-guide` — guida Google ai prompt per immagini (aggiornata il 9 settembre 2026)
- `https://cloud.google.com/blog/products/ai-machine-learning/ultimate-prompting-guide-for-nano-banana` — Google Cloud, 6 marzo 2026
- `https://docs.bfl.ai/guides/prompting_summary` e `https://docs.bfl.ai/guides/prompting_unified_basics` — Black Forest Labs
- `https://docs.ideogram.ai/using-ideogram/generation-settings/negative-prompt` — Ideogram

Non aperte (risposta 403), usate solo tramite gli estratti dei risultati di ricerca e quindi come conferma, non come fonte: la documentazione di Midjourney (`docs.midjourney.com`) e la guida ai prompt di Adobe Firefly (`helpx.adobe.com`).

Letteratura accademica recente sul prompting per immagini: non ancora consultata. Quando entra, va in questa sezione, e dove contraddice un principio sopra si decide caso per caso.
