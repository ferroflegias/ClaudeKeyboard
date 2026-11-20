# ClaudeKeyboard — Piano di progetto

Tastiera personalizzata per iOS (italiano e inglese) che porta Claude dentro qualsiasi app (Mail, Messaggi, WhatsApp…), per riscrivere, correggere, tradurre o rispondere senza cambiare app.

## Decisioni prese
| Tema | Decisione |
|------|-----------|
| Lingue tastiera | Italiano + Inglese |
| Distribuzione | Solo uso personale, nessuna pubblicazione su App Store |
| Budget | Costo zero (o il più vicino possibile a zero) |
| iPad | Non previsto |

| Integrazione | **Opzione B** (app Claude + Comandi rapidi) come primo passo; tastiera con API (Opzione A) solo se servirà. Guida: [`comandi-rapidi/GUIDA.md`](comandi-rapidi/GUIDA.md) |

---

## 1. Obiettivi
| # | Obiettivo | Misura di successo |
|---|-----------|--------------------|
| 1 | Usare Claude senza lasciare l'app in cui si scrive | Azione completa in < 3 tap |
| 2 | Tastiera usabile anche senza AI | Digitazione IT/EN completa (accenti, maiuscole, numeri, punteggiatura) |
| 3 | Costo nullo o trascurabile | Nessun abbonamento a pagamento aggiuntivo |
| 4 | Privacy | Il testo va a Claude solo su azione esplicita |

---

## 2. Come collegarsi a Claude: le opzioni

### Si può usare l'app Claude installata sull'iPhone?
**Solo in parte.** L'app Claude per iOS offre un'azione **"Ask Claude"** (App Intent) utilizzabile da Comandi rapidi (Shortcuts), Siri, Spotlight e dal menu Condividi, e usa il piano del tuo account Claude (anche gratuito, entro i limiti d'uso). Però:
- un'**estensione tastiera non può eseguire un Comando rapido né un App Intent e ricevere la risposta**: iOS non lo consente;
- l'unica via è aprire l'app Comandi con un URL, ma una tastiera non può aprire URL in modo supportato (esistono trucchi poco affidabili) e il ritorno porterebbe comunque fuori dall'app in cui stai scrivendo;
- usare le credenziali di login di claude.ai dentro un'app propria non è un'integrazione supportata (contro i termini d'uso, e si rompe facilmente).

Conclusione: **per avere Claude *dentro* la tastiera serve l'API.** Con l'app Claude si può fare un flusso alternativo, senza costi ma con meno integrazione.

### Opzione A — Tastiera con API (integrazione completa)
- La tastiera chiama direttamente l'API Anthropic (`POST /v1/messages`, streaming).
- **Costo:** l'API è a consumo e separata dagli abbonamenti Claude (non è inclusa né nel piano gratuito né in Pro). Serve una ricarica iniziale minima di credito (pochi dollari) sulla Anthropic Console.
- **Stima d'uso** con **Claude Haiku 5.5** ($0,10 / $0,50 per milione di token in ingresso/uscita): una riscrittura tipica costa circa **$0,0003** → ~1.000 azioni ≈ $0,30. Una ricarica minima dura anni con uso personale.
- Modello configurabile: Claude Opus 5.5 dà qualità superiore ma costa circa 30–40 volte di più per azione (ancora circa 1 centesimo).
- Imposta un **limite di spesa** nella Console per evitare sorprese.

### Opzione B — App Claude + Comandi rapidi (zero costi, zero o poco codice)
- Si creano dei Comandi rapidi ("Migliora testo", "Traduci in inglese", "Rendi formale"…) che usano l'azione **Ask Claude** con un prompt fisso.
- Si avviano su testo selezionato dal **menu Condividi**, oppure dal **Tocco posteriore** (Impostazioni › Accessibilità › Tocco) o dal tasto Azione, leggendo il testo dagli appunti.
- Il risultato viene copiato negli appunti; si incolla nel campo.
- **Pro:** gratuito, usa il tuo account Claude, prototipo pronto in 15 minuti, nessuna build Xcode.
- **Contro:** più passaggi (seleziona → condividi/tocco → incolla), meno fluido; dipende da come l'app Claude esegue l'azione (alcune versioni potrebbero aprire l'app).
- La tastiera personalizzata in questo scenario è facoltativa: al massimo un tasto "Incolla risultato" e preset di prompt.

### Opzione C — Ibrida (consigliata da provare)
1. Partire subito con l'**Opzione B** per capire quali azioni usi davvero (costo zero).
2. Se il flusso con gli appunti risulta scomodo, sviluppare la tastiera con l'**Opzione A** usando Haiku 5.5, con spesa di pochi centesimi al mese.

---

## 3. Vincoli di sviluppo a costo zero

| Vincolo | Impatto | Soluzione |
|---------|---------|-----------|
| Serve un **Mac con Xcode** (gratuito) | Senza Mac non si compila | Usare un Mac proprio o in prestito |
| **Apple ID gratuito** (senza Developer Program da 99 €/anno) | L'app installata **scade dopo 7 giorni** e va reinstallata da Xcode | Reinstallazione settimanale (1 minuto con iPhone collegato) |
| Limiti dell'account gratuito su App ID e capability (es. App Groups) | Configurazione condivisa tra app e tastiera potrebbe dare problemi | Progetto che **non dipende da App Groups**: chiave API in un file di configurazione locale escluso da git, impostazioni nella tastiera stessa |
| Rete solo con **"Accesso completo"** | Senza, niente Claude | Attivarlo una volta nelle Impostazioni |
| Memoria dell'estensione limitata (~50–70 MB) | Crash se si esagera | UI in UIKit, nessuna libreria pesante |
| Testo leggibile solo attorno al cursore (`textDocumentProxy`) | Non si legge l'intera mail | Lavorare su selezione / testo prima del cursore; "Rispondi a…" via appunti |
| Niente microfono nelle estensioni | No dettatura | Fuori ambito |

Senza App Store non servono privacy policy, recensione Apple né rispetto delle linee guida di pubblicazione.

---

## 4. Funzionalità (Opzione A)

### MVP
- **Tastiera QWERTY bilingue IT/EN**
  - Tasto lingua per passare da italiano a inglese (influenza suggerimenti e correttore).
  - Accenti con pressione prolungata (à è é ì ò ù; tasto rapido per "è" in italiano).
  - Maiuscole, blocco maiuscole, numeri/simboli, cancellazione continua, tasto globo.
  - Suggerimenti di parola con `UITextChecker` nella lingua attiva.
- **Barra Claude** sul testo selezionato (o sul testo prima del cursore):
  - Migliora/correggi · Formale · Informale · Accorcia · Espandi · Traduci IT⇄EN
  - **Chiedi a Claude**: richiesta libera
- **Anteprima in streaming** con *Sostituisci*, *Inserisci*, *Rigenera*, *Annulla*.
- **App contenitore minima**: istruzioni per attivare tastiera e Accesso completo.

### Dopo l'MVP
- "Rispondi a…" dal messaggio copiato negli appunti.
- Preset di prompt personali e profilo di stile ("dammi del tu", firma).
- Cronologia locale delle ultime generazioni.

---

## 5. Architettura (Opzione A)

```
┌──────────────────── iPhone ────────────────────┐
│  App contenitore      Estensione tastiera      │
│  (istruzioni)         - Tasti IT/EN            │
│                       - Barra Claude/anteprima │
│                       - ClaudeClient (HTTP+SSE)│
└──────────────────────────────┼─────────────────┘
                               │ HTTPS
                               ▼
                 Claude API (POST /v1/messages)
```

- **Client:** `URLSession` con streaming SSE (non esiste un SDK Anthropic ufficiale per Swift).
- **Modello predefinito:** `claude-haiku-5-5` con effort basso, per costo minimo e velocità; Opus 5.5 selezionabile.
- **Prompt:** system prompt fisso ("Sei un assistente di scrittura in una tastiera. Restituisci solo il testo finale…") + istruzione dell'azione + testo; stessa lingua del testo salvo traduzione.
- **Errori:** gestire `stop_reason` (incluso `refusal`), 401 chiave errata, 429/5xx con nuovo tentativo, rete assente.
- **Chiave API:** in `Secrets.xcconfig` escluso da git e compilata nell'app (accettabile perché l'app non viene distribuita); alternativa: Keychain.

---

## 6. Piano di lavoro (Opzione A, 1 persona, tempo libero)

| Fase | Durata | Attività | Risultato |
|------|--------|----------|-----------|
| 0. Prova Opzione B | 1 sera | Comandi rapidi con "Ask Claude" | Capire quali azioni servono davvero |
| 1. Setup | 1–2 sere | Progetto Xcode (app + estensione), firma con Apple ID gratuito, installazione su iPhone | Tastiera vuota funzionante |
| 2. Tastiera base | 1–2 sett. | Layout, accenti, simboli, cambio lingua IT/EN, suggerimenti | Tastiera usabile ogni giorno |
| 3. Client Claude | 3–4 sere | Chiamata in streaming, errori, annullamento | Collegamento funzionante |
| 4. Barra Claude | 1 sett. | Azioni rapide, "Chiedi", anteprima, sostituzione testo | **MVP** |
| 5. Rifinitura | continuo | Test in Mail/WhatsApp/Messaggi, memoria, preset | Uso quotidiano |

---

## 7. Rischi
| Rischio | Mitigazione |
|---------|-------------|
| Reinstallazione ogni 7 giorni scomoda | Promemoria settimanale; eventuale Developer Program in futuro |
| Limiti account gratuito su capability | Architettura senza App Groups |
| Memoria dell'estensione | UI leggera, profilazione con Instruments |
| Spesa API imprevista | Haiku 5.5 di default + limite di spesa nella Console |

---

## 8. Prossimi passi
1. ✅ Scelta l'Opzione B.
2. Creare il comando "Claude Testo" seguendo `comandi-rapidi/GUIDA.md`.
3. Usarlo per 1–2 settimane annotando azioni più usate e punti scomodi.
4. Valutare se passare all'Opzione A (serve un Mac).
