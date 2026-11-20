# Claude Testo — Comando rapido per iPhone

Un unico Comando rapido con un menu di azioni (migliora, cambia tono, traduci, rispondi…).
Usa l'app Claude già installata e il tuo account: **nessun costo, nessuna API, nessun codice**.

**Requisiti:** iOS 18 o successivo; app Claude installata e con accesso effettuato.

## Come si usa (dopo averlo creato)
- **Dal menu Condividi:** seleziona il testo → *Condividi* → **Claude Testo** → scegli l'azione → il risultato finisce negli appunti → tieni premuto nel campo → *Incolla*.
- **Con il Tocco posteriore:** copia il testo → doppio tocco sul retro dell'iPhone → scegli l'azione → *Incolla*.
- **Per scrivere da zero** ("Chiedi a Claude"): avvia il comando senza testo copiato e scrivi la richiesta.

---

## Passo 1 — Crea il comando
1. Apri l'app **Comandi** → **+** in alto a destra.
2. Rinominalo **Claude Testo** (tocca il nome in alto). Scegli un'icona a piacere.
3. Tocca la **ⓘ** (o il menu a tendina del nome) → attiva **Mostra nel Foglio di condivisione**. In *Tipi di input* lascia solo **Testo**.
4. In cima apparirà il blocco *"Ricevi Testo da Foglio di condivisione"*: tocca *"Se non c'è input"* e scegli **Continua**.

## Passo 2 — Recupera il testo
Aggiungi le azioni nell'ordine (usa la barra di ricerca in basso per trovarle):

1. **Se** → condizione: *Input comando rapido* **ha un valore qualsiasi**
   - dentro *Se*: **Imposta variabile** → nome `Testo`, valore *Input comando rapido*
   - dentro *Altrimenti*: **Ottieni appunti**, poi **Imposta variabile** → nome `Testo`, valore *Appunti*
   - *Fine se*

## Passo 3 — Il menu delle azioni
2. **Scegli dal menu** → titolo `Cosa faccio?` → crea queste voci (i nomi sono liberi):

| Voce del menu | Dentro la voce aggiungi **Testo** con questo contenuto, poi **Imposta variabile** `Istruzione` = *Testo* |
|---|---|
| ✏️ Migliora | `Correggi errori di ortografia, grammatica e punteggiatura e migliora la scorrevolezza, mantenendo significato, tono e lingua originali.` |
| 🎩 Più formale | `Riscrivi il testo con un tono più formale e professionale, adatto a una mail di lavoro. Mantieni la lingua originale.` |
| 😊 Più informale | `Riscrivi il testo con un tono più informale, cordiale e naturale. Mantieni la lingua originale.` |
| ✂️ Accorcia | `Riscrivi il testo in modo più breve e diretto, mantenendo tutte le informazioni importanti. Mantieni la lingua originale.` |
| ➕ Espandi | `Sviluppa il testo in modo più completo e articolato, senza inventare fatti nuovi. Mantieni la lingua originale.` |
| 🇬🇧 Traduci in inglese | `Traduci il testo in inglese naturale, mantenendo tono e registro.` |
| 🇮🇹 Traduci in italiano | `Traduci il testo in italiano naturale, mantenendo tono e registro.` |
| 💬 Rispondi | `Il testo è un messaggio che ho ricevuto. Scrivi una mia risposta breve, cortese e pertinente, nella stessa lingua del messaggio.` |
| ❓ Chiedi a Claude | Qui invece: **Chiedi input** (tipo *Testo*, domanda `Cosa devo scrivere?`), poi **Imposta variabile** `Istruzione` = *Input fornito* |

## Passo 4 — Componi la richiesta e chiedi a Claude
Dopo *Fine menu*:

3. **Testo** con questo contenuto (inserisci le variabili `Istruzione` e `Testo` dove indicato, toccando *Variabili* sopra la tastiera):

```
Sei un assistente di scrittura. Esegui questa istruzione sul testo qui sotto.

Istruzione: [Istruzione]

Testo:
"""
[Testo]
"""

Rispondi SOLO con il testo finale, pronto da incollare: niente introduzioni, commenti, spiegazioni o virgolette.
```

4. **Ask Claude** (azione dell'app Claude; cercando "Claude" la trovi) → come messaggio inserisci il *Testo* del passo precedente.
5. **Copia negli appunti** → *Risposta di Claude* (l'output dell'azione precedente).
6. **Mostra notifica** → `Copiato ✅ — tieni premuto e Incolla`.

> Facoltativo: al posto della notifica usa **Mostra risultato** per leggere il testo prima di incollarlo.

## Passo 5 — Tocco posteriore (consigliato)
*Impostazioni → Accessibilità → Tocco → Tocco posteriore → Doppio tocco →* **Claude Testo**.

Puoi anche assegnarlo al **tasto Azione** (iPhone 15 Pro e successivi): *Impostazioni → Tasto Azione → Comando rapido*.

---

## Prova
1. In Note scrivi: `ciao, domani non riesco a venire alla riunione, ci sentiamo la prossima settimana`.
2. Selezionalo → *Condividi* → **Claude Testo** → **🎩 Più formale**.
3. Incolla: dovresti ottenere una versione formale, senza testo aggiuntivo.

## Problemi comuni
| Problema | Soluzione |
|----------|-----------|
| "Claude Testo" non compare in Condividi | Scorri in fondo al foglio → *Modifica azioni* → aggiungilo ai preferiti |
| L'azione "Ask Claude" non si trova | Aggiorna l'app Claude, aprila almeno una volta e verifica di essere su iOS 18+ |
| Si apre l'app Claude invece di restare in background | Dipende dalla versione dell'app; il risultato viene comunque copiato. Torna indietro con il pulsante in alto a sinistra |
| Claude aggiunge frasi tipo "Ecco il testo:" | Rafforza l'ultima riga del prompt o usa *Mostra risultato* per controllare |
| Risultati lenti o limite raggiunto | Le richieste contano nei limiti d'uso del tuo piano Claude |

## Annotazioni per il futuro
Tieni nota di quali voci usi di più e di cosa risulta scomodo: servirà a decidere se passare alla tastiera con API (Opzione A del piano).
