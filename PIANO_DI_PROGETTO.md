# ClaudeKeyboard — Piano di progetto

Tastiera personalizzata per iOS che porta Claude dentro qualsiasi app (Mail, Messaggi, WhatsApp, Slack, Note…): scrivi, selezioni e chiedi a Claude di riscrivere, correggere, tradurre o rispondere, senza cambiare app.

---

## 1. Obiettivi

| # | Obiettivo | Misura di successo |
|---|-----------|--------------------|
| 1 | Usare Claude senza lasciare l'app in cui si scrive | Azione completa (seleziona → azione → inserisci) in < 3 tap |
| 2 | Risposta percepita come veloce | Primo testo visibile in < 1,5 s (streaming) |
| 3 | Tastiera usabile anche senza AI | Digitazione italiana completa (accenti, maiuscole, numeri, punteggiatura) |
| 4 | Privacy chiara | Nessun testo salvato su server propri; invio a Claude solo su azione esplicita |

**Fuori ambito (v1):** dettatura vocale (le estensioni tastiera non possono usare il microfono), lettura dell'intero contenuto della mail/chat (iOS espone solo il testo attorno al cursore), autocorrezione avanzata stile Apple.

---

## 2. Funzionalità

### MVP (v1.0)
- **Tastiera QWERTY italiana**: maiuscole, numeri/simboli, accenti con pressione prolungata, tasto "globo" per cambiare tastiera, cancellazione continua, suggerimenti base (`UITextChecker` + `UILexicon`).
- **Barra Claude** sopra i tasti con azioni rapide sul testo selezionato (o, in assenza di selezione, sul testo prima del cursore):
  - ✏️ Migliora / correggi
  - 🎩 Tono formale · 😊 Tono informale
  - ✂️ Accorcia · ➕ Espandi
  - 🌍 Traduci (IT ⇄ EN, lingua configurabile)
  - 💬 **Chiedi a Claude**: campo libero ("scrivi una risposta che declina gentilmente l'invito")
- **Anteprima del risultato** in streaming, con pulsanti *Sostituisci*, *Inserisci*, *Rigenera*, *Annulla*.
- **App contenitore** (obbligatoria su iOS): onboarding per attivare la tastiera e l'"Accesso completo", configurazione chiave API, scelta lingua e modello.

### v1.1 – v1.2
- **Rispondi a…**: incolla dagli appunti il messaggio ricevuto e genera una risposta.
- **Preset personalizzati** (prompt salvati: "firma aziendale", "risposta cliente"…), sincronizzati via App Group.
- **Profilo di stile**: istruzioni persistenti ("dammi del tu", "firma sempre Marco").
- Cronologia locale delle ultime generazioni (solo sul dispositivo, cancellabile).
- Tema chiaro/scuro, feedback aptico, layout per iPad.

### v2 (eventuale)
- Backend proprio con account (Sign in with Apple) e abbonamento in-app, per distribuire l'app senza che l'utente debba avere una chiave API.
- Più lingue di tastiera; suggerimenti contestuali di completamento.

---

## 3. Vincoli della piattaforma iOS (da tenere presenti dal giorno 1)

| Vincolo | Impatto | Soluzione |
|---------|---------|-----------|
| Rete disponibile solo con **"Accesso completo"** (`RequestsOpenAccess = YES`) | Senza, niente Claude | Onboarding che spiega perché; la tastiera deve comunque funzionare senza (requisito App Store 4.4.1) e mostrare un avviso nella barra Claude |
| **Limite di memoria** dell'estensione (~50–70 MB) | Crash se si usa troppa RAM | UI leggera (UIKit o SwiftUI minimale), niente librerie pesanti, nessun modello locale |
| Accesso al testo solo tramite `textDocumentProxy` (testo prima/dopo il cursore, selezione) | Non si legge l'intera mail | Lavorare su selezione / testo vicino al cursore; "Rispondi a…" via appunti |
| Campi password e tastierini numerici | iOS forza la tastiera di sistema | Nessuna azione necessaria |
| Niente microfono nelle estensioni | No dettatura | Fuori ambito |
| L'estensione è un processo separato dall'app | Impostazioni e chiave condivise | **App Group** (impostazioni) + **Keychain condiviso** (chiave API) |
| Build e firma richiedono **macOS + Xcode** e un Apple Developer Program (99 €/anno) | Non compilabile in ambienti Linux | Sviluppo su Mac; test su dispositivo reale |

---

## 4. Architettura

```
┌────────────────────────── iPhone ──────────────────────────┐
│                                                             │
│  App contenitore (SwiftUI)        Estensione tastiera       │
│  - Onboarding                     (UIInputViewController)   │
│  - Impostazioni, preset           - Layout tasti            │
│  - Inserimento chiave API         - Barra Claude + anteprima│
│            │                                │               │
│            └──── App Group (UserDefaults) ──┤               │
│            └──── Keychain condiviso ────────┤               │
│                                             │               │
│                  ClaudeKit (Swift Package condiviso)        │
│                  - Client HTTP + streaming SSE              │
│                  - Prompt delle azioni                      │
└─────────────────────────────────────────────┼───────────────┘
                                              │ HTTPS
                       MVP: diretto ──────────┤
                       v2: tramite backend ───┤
                                              ▼
                              Claude API (POST /v1/messages)
```

### Moduli
1. **KeyboardUI** — layout dei tasti, gestione tocchi, pressione prolungata, cambio pagine, suggerimenti.
2. **ClaudeBar** — azioni rapide, campo "Chiedi", pannello anteprima con streaming.
3. **ClaudeKit** (Swift Package) — chiamata all'API Messages via `URLSession` con streaming SSE (`URLSession.bytes`), gestione errori/ritentativi, cancellazione della richiesta.
4. **Settings/Storage** — App Group + Keychain, modelli dati dei preset.
5. **HostApp** — onboarding, impostazioni, privacy policy.

### Integrazione con Claude
- **Endpoint:** `POST https://api.anthropic.com/v1/messages` con `stream: true`. Non esiste un SDK Anthropic ufficiale per Swift: si usa HTTP diretto (`URLSession`), con un piccolo client interno.
- **Modello predefinito:** `claude-opus-5-5`, con `output_config.effort: "low"` per le azioni rapide (riscrittura, traduzione) così da ridurre la latenza; modello e effort configurabili nelle impostazioni (es. Claude Haiku 5.5 per chi preferisce massima velocità e minor costo — da valutare con test reali).
- **Prompt:** un system prompt stabile per tutte le azioni ("Sei un assistente di scrittura dentro una tastiera. Restituisci solo il testo finale, senza commenti né virgolette…") + istruzione specifica dell'azione + testo dell'utente. System prompt identico per sfruttare il **prompt caching**.
- **`max_tokens`** moderato (es. 2.000) — i testi da tastiera sono brevi.
- **Gestione risposte:** controllare sempre `stop_reason` (incluso `refusal`, da mostrare con un messaggio chiaro), errori 401 (chiave errata), 429 (limiti), 529/5xx (ritentare con backoff), timeout di rete.
- **Cancellazione:** se l'utente chiude il pannello o cambia app, la richiesta viene annullata.

### Gestione della chiave API
- **MVP (uso personale / TestFlight):** l'utente inserisce la propria chiave Anthropic nell'app contenitore; salvata nel **Keychain condiviso**, mai in UserDefaults né nel codice.
- **v2 (App Store pubblico):** backend proxy leggero (es. Cloudflare Workers / AWS Lambda) che custodisce la chiave, autentica l'utente (Sign in with Apple), applica limiti d'uso e verifica l'abbonamento (StoreKit 2). Nessuna chiave nell'app.

---

## 5. Privacy e conformità
- Il testo viene inviato a Claude **solo** quando l'utente tocca un'azione; nessun keylogging, nessuna telemetria sul testo digitato.
- Nessun salvataggio lato server (MVP: nessun server). Cronologia solo locale e opzionale.
- Privacy policy e dichiarazioni "App Privacy" su App Store Connect coerenti con l'invio dei dati ad Anthropic.
- Requisiti App Store per tastiere (linea guida 4.4.1): tasto per passare alla tastiera successiva, funzionamento senza accesso completo, raccolta dati solo per migliorare la tastiera.
- Accessibilità: VoiceOver sui tasti e sulla barra Claude, Dynamic Type nell'app contenitore.

---

## 6. Piano di lavoro

Stima indicativa per **1 sviluppatore iOS**, part-time esclusa.

| Fase | Durata | Attività | Deliverable |
|------|--------|----------|-------------|
| **0. Setup** | 2–3 gg | Progetto Xcode (app + Keyboard Extension), App Group, Keychain sharing, Swift Package `ClaudeKit`, repo e CI (Xcode Cloud o GitHub Actions su macOS) | Progetto che compila e installa una tastiera vuota |
| **1. Spike tecnico** | 3 gg | Chiamata in streaming dall'estensione, misura memoria e latenza, prova di `textDocumentProxy` in Mail/Messaggi/WhatsApp | Report rischi; conferma fattibilità |
| **2. Tastiera base** | 2 sett. | Layout IT, maiuscole, simboli, accenti, cancellazione, cambio tastiera, suggerimenti base, dark mode | Tastiera usabile quotidianamente |
| **3. ClaudeKit** | 1 sett. | Client HTTP + SSE, modelli richiesta/risposta, errori, ritentativi, cancellazione, test unitari con risposte simulate | Libreria testata |
| **4. Barra Claude** | 2 sett. | Azioni rapide, campo "Chiedi", anteprima in streaming, Sostituisci/Inserisci/Rigenera, gestione selezione | **MVP funzionante** |
| **5. App contenitore** | 1 sett. | Onboarding guidato, inserimento chiave, impostazioni, privacy policy | App completa |
| **6. Test e rifinitura** | 1–2 sett. | Test su più app e dispositivi, memoria, rete lenta/assente, accessibilità, TestFlight con 5–10 tester | Beta su TestFlight |
| **7. v1.1** | 2 sett. | "Rispondi a…", preset, profilo di stile, cronologia | Release 1.1 |
| **8. v2 (opzionale)** | 3–4 sett. | Backend proxy, Sign in with Apple, abbonamento StoreKit 2, invio ad App Review | Pubblicazione App Store |

**Totale MVP su TestFlight: ~8–9 settimane. Con v2 pubblica: ~14–15 settimane.**

---

## 7. Rischi principali

| Rischio | Probabilità | Mitigazione |
|---------|-------------|-------------|
| Superamento limite memoria dell'estensione | Media | Profilazione con Instruments fin dallo spike; UI minimale |
| Latenza percepita alta | Media | Streaming, effort basso, prompt caching, modello configurabile |
| Utenti diffidenti verso "Accesso completo" | Alta | Onboarding trasparente, privacy policy chiara, tastiera utile anche senza |
| Comportamento diverso di `textDocumentProxy` tra app | Media | Matrice di test (Mail, Messaggi, WhatsApp, Gmail, Outlook, Slack, Note) |
| Rifiuto in App Review | Media | Rispetto linea guida 4.4.1; niente chiave API nel binario |
| Costi API (in v2 a carico tuo) | Media | Limiti per utente nel backend, abbonamento, monitoraggio consumi |

---

## 8. Strumenti e prerequisiti
- Mac con Xcode recente, iPhone di test, account **Apple Developer Program**.
- Account **Anthropic Console** con chiave API e limiti di spesa impostati.
- Swift 5.10+/6, SwiftUI (app) + UIKit (tasti, per prestazioni), deployment target iOS 17+.
- Nessuna dipendenza esterna obbligatoria.

---

## 9. Decisioni aperte
1. **Distribuzione:** uso personale/TestFlight con chiave propria (più semplice) o App Store pubblico con backend e abbonamento?
2. **Modello predefinito:** Claude Opus 5.5 con effort basso vs. Claude Haiku 5.5 (più rapido ed economico) — decidere dopo lo spike misurando qualità e latenza in italiano.
3. **Lingue della tastiera** oltre all'italiano nella v1.
4. **iPad** nella v1 o successivamente.

---

## 10. Prossimi passi
1. Rispondere alle decisioni aperte (soprattutto la n. 1).
2. Creare il progetto Xcode (Fase 0) e la struttura del repository.
3. Eseguire lo spike tecnico (Fase 1) e aggiornare questo piano con i risultati.
