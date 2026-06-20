# 🦅 Italy Team CC — Ottimizzatore

Web app per la gestione settimanale del Campionato per Club (CC) in Athletics Championship, per il club **Italy Team**.

**Link:** `https://sergiocosi.github.io/italy`

---

## Cosa fa

- **Inventario**: ogni giocatore inserisce e mantiene aggiornati atleti, allenatori, centri di allenamento, attrezzatura (maglia/pantaloncini/scarpe per disciplina) e risorse (monete, gemme, AC, CT)
- **Dati CC**: inserimento settimanale di Valore CC per disciplina, Allenatore per gruppo (in comodo doppione rispetto all'Inventario) e discipline obbligatorie del giorno
- Calcola il **ranking** per disciplina e suggerisce le **discipline libere ottimali** per ogni giocatore
- Compone le **griglie staffetta** (4×100 e 4×400) con metodo equilibrato, top-heavy o bilanciato ottimale, con possibilità di override manuale da parte dell'admin
- Genera il **messaggio del giorno** pronto da copiare su WhatsApp/Telegram, con staffette e libere consigliate
- **Trofei**: tracking dei Personal Best per tutte le 19 discipline (bozza personale + snapshot settimanale pubblicato dall'admin)
- **Scambi**: bacheca per scambio carte tra membri del club
- Link rapido al **DC Coach Tracker** (`https://sergiocosi.github.io/dctracker/`), tool collegato per l'analisi dei match Daily Challenge

L'app non ha più dati statici da modificare nel codice: tutto viene inserito dagli utenti tramite l'interfaccia e salvato su Supabase.

---

## Architettura

- **Frontend**: singolo file `index.html` (vanilla HTML/CSS/JS), nessuna build, hostato su GitHub Pages
- **Backend**: Supabase (stesso progetto condiviso con il DC Coach Tracker)
- **Autenticazione**: selezione nome giocatore + password; sessione persistita in `localStorage`. L'admin può agire per conto di qualsiasi giocatore tramite selettore dedicato in ogni sezione

### Tabelle Supabase

| Tabella | Contenuto |
|---|---|
| `italy_players` | Elenco giocatori del club |
| `italy_weeks` | Settimane CC (una riga attiva alla volta) |
| `italy_inventory` | Inventario per giocatore: atleti, allenatori, centri, attrezzatura, risorse (~348 colonne) |
| `italy_rankings` | Rank CC per disciplina e valori allenatore per gruppo, per settimana |
| `italy_obbligatorie` | Discipline obbligatorie assegnate per giorno/giocatore |
| `italy_cards` | Progressione carte per gli upgrade (tab Progressione) |
| `italy_scambi` | Inserzioni della bacheca scambi |
| `italy_trofei` | Snapshot settimanali pubblicati di trofei e PB |
| `italy_pb_drafts` | Bozze personali di PB, aggiornabili liberamente |
| `italy_messaggi` | Messaggi del giorno pubblicati dall'admin |

Tutte le tabelle hanno RLS attiva: lettura pubblica, scrittura autenticata via anon key.

---

## Aggiornamento del database

Non c'è più un blocco dati da editare nel file: l'aggiornamento settimanale avviene **dagli utenti, dentro l'app**:

1. Ogni giocatore inserisce Valore CC, Allenatore e discipline obbligatorie nel tab **Dati CC** (o nell'Inventario per atleti/centri/attrezzatura)
2. L'admin, dal tab **Giorno CC**, genera e pubblica il messaggio con staffette e libere
3. L'admin può intervenire per conto di qualsiasi giocatore tramite il selettore admin presente in ogni sezione

Per interventi diretti sul database (correzioni, import massivi, manutenzione), si opera via SQL su Supabase.

---

## Deploy

Il sito è statico: ogni modifica a `index.html` va semplicemente caricata su GitHub.

1. Apri la repo su `github.com/sergiocosi/italy`
2. Sostituisci `index.html` (upload diretto, o tramite l'editor di GitHub)
3. Commit changes
4. Dopo circa un minuto GitHub Pages pubblica la nuova versione

---

## Logica dell'algoritmo staffette/libere

Le libere vengono assegnate con un algoritmo a tre fasi: greedy sui giocatori TOP, poi greedy sui restanti, poi una passata di copertura per le discipline ancora scoperte. Il sistema di punteggio è quello reale del CC: 120 punti al 1°, 116 al 2°, 112 al 3°, poi -4 per ogni posizione successiva.

Le griglie staffetta possono essere generate in tre modalità (equilibrate, top-heavy, bilanciato ottimale) e l'admin può sempre sovrascriverle manualmente con drag-and-drop; l'override viene mantenuto e riflesso nel messaggio pubblicato.

---

## Note

- Il file funziona offline una volta caricato, ma richiede HTTPS per funzionare su iOS Safari — usare sempre il link GitHub Pages, non il file locale
- Per la guida d'uso pensata per i membri del club, vedi il tab **Guida** dentro l'app stessa
