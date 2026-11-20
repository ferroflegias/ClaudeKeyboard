# Comandi rapidi Claude per iPhone

Due Comandi rapidi che usano l'app Claude già installata e il tuo account: **nessun costo, nessuna API, nessun codice**.

| Comando | A cosa serve | Risultato |
|---------|--------------|-----------|
| **Claude Testo** | Testo selezionato o copiato: migliora, tono, riassumi, traduci IT/EN/ES, chiedi | Negli appunti (Riassumi: in una finestra) |
| **Riassumi pagina** | Intera pagina web condivisa da Safari, senza selezionare nulla | In una finestra, con opzioni Copia / Continua in Claude |

**Requisiti:** iOS 18 o successivo; app Claude installata e con accesso effettuato.

---

# Parte 1 — Claude Testo

## Come si usa
- **Dal menu Condividi:** seleziona il testo → *Condividi* → **Claude Testo** → scegli l'azione → *Incolla*.
- **Con il Tocco posteriore:** copia il testo → doppio tocco sul retro → scegli l'azione → *Incolla*.
- **Per scrivere da zero:** avvia il comando senza testo copiato → *Chiedi a Claude*.

## Passo 1 — Crea il comando
1. App **Comandi** → **+**.
2. Rinominalo **Claude Testo**.
3. **ⓘ** → attiva **Mostra nel Foglio di condivisione**; in *Tipi di input* lascia solo **Testo**.
4. Nel blocco *"Ricevi Testo da Foglio di condivisione"* → *"Se non c'è input"* → **Continua**.

## Passo 2 — Recupera il testo
1. **Se** → *Input comando rapido* **ha un valore qualsiasi**
   - dentro *Se*: **Imposta variabile** `Testo` = *Input comando rapido*
   - dentro *Altrimenti*: **Ottieni appunti** → **Imposta variabile** `Testo` = *Appunti*
   - *Fine se*

## Passo 3 — Il menu delle azioni
2. **Scegli dal menu** → titolo `Cosa faccio?`. Per ogni voce (salvo dove indicato): **Testo** con l'istruzione → **Imposta variabile** `Istruzione` = *Testo*.

**✏️ Migliora**
```
Correggi errori di ortografia, grammatica e punteggiatura e migliora la scorrevolezza, mantenendo significato, tono e lingua originali.
```
**🎩 Più formale**
```
Riscrivi il testo con un tono più formale e professionale, adatto a una mail di lavoro. Mantieni la lingua originale.
```
**😊 Più informale**
```
Riscrivi il testo con un tono più informale, cordiale e naturale. Mantieni la lingua originale.
```
**📝 Riassumi** — azioni in quest'ordine:
1. **Crea Markdown da rich text** (input: `Testo`)
2. **Imposta variabile** `Testo` = *Markdown*
3. **Testo**:
```
Il testo può essere in Markdown, copiato da una pagina web: ignora menu, pulsanti, pubblicità e note a piè di pagina. Riassumi in italiano i contenuti principali: una frase iniziale con il punto chiave, poi da 3 a 6 punti elenco che iniziano con "• ". Non usare la sintassi Markdown nella risposta (niente #, ** o link).
```
4. **Imposta variabile** `Istruzione` = *Testo*
5. **Testo**: `finestra` → **Imposta variabile** `Modalità` = *Testo*

**🇬🇧 Traduci in inglese**
```
Traduci il testo in inglese naturale, mantenendo tono e registro.
```
**🇮🇹 Traduci in italiano**
```
Traduci il testo in italiano naturale, mantenendo tono e registro.
```
**🇪🇸 Traduci in spagnolo**
```
Traduci il testo in spagnolo naturale, mantenendo tono e registro.
```
**❓ Chiedi a Claude** — **Chiedi input** (Testo, `Cosa devo scrivere?`) → **Imposta variabile** `Istruzione` = *Input fornito*

## Passo 4 — Chiedi a Claude e mostra il risultato
Dopo *Fine menu*:

3. **Testo** (inserisci le variabili `Istruzione` e `Testo`):
```
Sei un assistente di scrittura. Esegui questa istruzione sul testo qui sotto.

Istruzione: [Istruzione]

Testo:
"""
[Testo]
"""

Rispondi SOLO con il testo finale, pronto da incollare: niente introduzioni, commenti, spiegazioni o virgolette.
```
4. **Ask Claude** → messaggio: il *Testo* precedente.
5. **Imposta variabile** `Risultato` = *Risposta di Claude*
6. **Se** → `Modalità` **è** `finestra`
   - dentro *Se*: le azioni della sezione **"Finestra del risultato"** qui sotto
   - dentro *Altrimenti*: **Copia negli appunti** `Risultato` → **Mostra notifica** `Copiato ✅ — tieni premuto e Incolla`
   - *Fine se*

## Passo 5 — Tocco posteriore / tasto Azione
*Impostazioni → Accessibilità → Tocco → Tocco posteriore → Doppio tocco →* **Claude Testo**
(oppure *Impostazioni → Tasto Azione → Comando rapido*).

---

# Parte 2 — Riassumi pagina

## Come si usa
In Safari, **senza selezionare testo**: *Condividi* → **Riassumi pagina** → leggi il riassunto nella finestra → scegli *Copia*, *Continua in Claude* o *Chiudi*.

## Passo 1 — Crea il comando
1. App **Comandi** → **+** → rinominalo **Riassumi pagina**.
2. **ⓘ** → attiva **Mostra nel Foglio di condivisione**; in *Tipi di input* lascia solo **URL** e **Pagine web di Safari**.
3. *"Se non c'è input"* → **Interrompi**.

## Passo 2 — Estrai il contenuto della pagina
1. **Ottieni articolo con Safari Reader** (input: *Input comando rapido*) — in inglese *Get Article using Safari Reader*.
2. **Ottieni dettagli di Articolo** → **Titolo** → **Imposta variabile** `Titolo`
3. **Ottieni dettagli di Articolo** → **Corpo** (tocca il parametro e scegli *Corpo*)
4. **Crea Markdown da rich text** (input: *Corpo*) → **Imposta variabile** `Pagina` = *Markdown*

## Passo 3 — Chiedi a Claude
5. **Testo** (inserisci le variabili `Titolo`, `Input comando rapido` e `Pagina`):
```
Riassumi in italiano questa pagina web, anche se è in un'altra lingua.

Formato:
- una frase iniziale con il punto chiave;
- da 4 a 8 punti elenco che iniziano con "• ", con fatti, numeri e conclusioni importanti;
- se presenti, una riga finale "Da notare:" con limiti, opinioni o aspetti controversi.

Usa testo semplice, senza sintassi Markdown (niente #, ** o link). Ignora menu, pubblicità, commenti e note.

Titolo: [Titolo]
Indirizzo: [Input comando rapido]

Contenuto (Markdown):
"""
[Pagina]
"""
```
6. **Ask Claude** → messaggio: il *Testo* precedente.
7. **Imposta variabile** `Risultato` = *Risposta di Claude*
8. Le azioni della sezione **"Finestra del risultato"** qui sotto.

---

# Finestra del risultato (usata da entrambi i comandi)
1. **Mostra risultato** → `Risultato`
   (la finestra resta aperta finché tocchi *Fine*; in alternativa usa **Anteprima** per una vista a schermo intero con pulsante di condivisione)
2. **Scegli dal menu** → titolo `E ora?` con tre voci:
   - **📋 Copia** → **Copia negli appunti** `Risultato` → **Mostra notifica** `Copiato ✅`
   - **💬 Continua in Claude** →
     1. **Testo**:
        ```
        Ecco un riassunto che mi hai preparato. Voglio approfondire.

        [Titolo]
        [Input comando rapido]

        [Risultato]
        ```
        (in *Claude Testo* le variabili `Titolo` e indirizzo non esistono: usa solo `[Risultato]`)
     2. **Copia negli appunti** → *Testo*
     3. **Apri app** → **Claude**
     4. **Mostra notifica** `Incolla nella chat e continua 💬`
   - **✅ Chiudi** → nessuna azione

> Perché non si apre direttamente la conversazione? L'azione "Ask Claude" non restituisce un collegamento alla conversazione e l'app Claude non offre link documentati per aprire o precompilare una chat normale (i link `claude://` documentati riguardano solo Claude Code). Per questo si copia il contesto e lo si incolla in una nuova chat.

---

## Prove
- **Claude Testo:** in Note scrivi `ciao, domani non riesco a venire alla riunione, ci sentiamo la prossima settimana` → seleziona → *Condividi* → **Claude Testo** → **🎩 Più formale** → *Incolla*.
- **Riassumi pagina:** apri un articolo di giornale in Safari → *Condividi* → **Riassumi pagina**.

## Problemi comuni
| Problema | Soluzione |
|----------|-----------|
| Il comando non compare in Condividi | In fondo al foglio → *Modifica azioni* → aggiungilo ai preferiti |
| "Ask Claude" non si trova | Aggiorna l'app Claude, aprila una volta, verifica iOS 18+; in italiano potrebbe chiamarsi "Chiedi a Claude" |
| Safari Reader dà errore (pagine che non sono articoli: home page, negozi…) | Sostituisci le azioni 1–3 del Passo 2 con **Ottieni contenuti della pagina web** (input: *Input comando rapido*) e usa il risultato in *Crea Markdown* |
| Pagina molto lunga: errore o risposta troncata | Seleziona solo la parte che ti interessa e usa *Claude Testo → Riassumi* |
| Si apre l'app Claude durante l'esecuzione | Dipende dalla versione dell'app; il comando continua comunque |
| Compaiono simboli come `\` o `**` | Togli *Crea Markdown da rich text* da quel comando |
| Limite d'uso raggiunto | Le richieste contano nei limiti del tuo piano Claude |
