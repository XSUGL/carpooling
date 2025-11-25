# Scheda Guida API REST

## PROGETTO 1: SISTEMA DI CARPOOLING

## 📝 DESCRIZIONE PROGETTO

Applicazione che collega automobilisti e passeggeri per condividere viaggi riducendo costi e impatto
ambientale.

**Funzionalità principali:**

- Registrazione/login per automobilisti e passeggeri  
- Creazione dei annunci
- Prenotazione e cancellazione di annunci  
- Dashboard per i passeggeri/automobilisti con elenco degli annunci  
- Notifiche per le conferme di annunci

---

## FASE 1: COMPRENSIONE DEL DOMINIO

### 1.1 Identificazione delle Entità Principali

**Domande da porsi:**

1. Quali sono gli "oggetti" principali del sistema?  
2. Quali caratteristiche ha ciascuna entità?  
3. Quali relazioni esistono tra le entità?

**Analisi:**

ENTITÀ IDENTIFICATE:

- Automobilista(id, nome, cognome, email, telefono, codice_fiscale, patente, data_nascita)

- Passeggero (id, nome, cognome, email, telefono, codice_fiscale)

- Annuncio (id, viaggio, partenza, destinazione, posti disponibili)

- Prenotazione (id,disponibilita,risposta)

**Relazioni:**

- Un automobilista può creare uno o piu annunci (0:N)

- Un annuncio deve essee creato da uno solo automobilista (1:1)

- Un passegere puo ricercare uno o piu annunci (0:N)

- Un passegere puo prenotare uno o piu annunci (0:N)

- Un annuncio puo essere ricercato da 1 o piu passeggeri (0:N)

- Un annuncio puo essere prenotato da 1 o piu passeggeri (0:N)

---

### 1.2 Identificazione degli Utenti e Ruoli

**Ruoli identificati:**

RUOLO: Passeggero

Permessi:

  - Registrarsi/Login

  - Visualizzare annunci disponibili

  - Consultare disponibilità di un annuncio

  - Prenotare annuncio

  - Visualizzare i propri prenotazioni

  - Cancellare le proprie prenotazioni (con regole temporali)

  - Modificare il proprio profilo

RUOLO: Automobilista

Permessi:

  - Login

  - Visualizzare dashboard con i propri annunci

  - Creare i annunci di viaggio

  - Modificare gli annunci di viaggio

  - Cancellare i annunci (con vincoli su eventuali prenotazioni esistenti)

  - Confermare/rifiutare prenotazioni

  - Visualizzare dettagli passeggero per prenotazione

  - Modificare il proprio profilo

RUOLO: Amministratore

Permessi:

  - Tutti i permessi dei ruoli precedenti

  - Gestire automobilisti e passeggeri

  - Visualizzare statistiche

---

## FASE 2: ANALISI DEI CASI D'USO

### 2.1 Elenco delle Operazioni per Entità

**Passeggeri:**

1. Registrarsi nel sistema  
2. Effettuare login  
3. Visualizzare lista annunci disponibili  
4. Visualizzare dettagli di un annuncio 
5. Consultare disponibilità di un annuncio 
6. Prenotare un annuncio 
7. Visualizzare i propri prenotazioni 
8. Visualizzare dettagli di una propria prenotazione  
9. Cancellare una propria prenotazione  
10. Modificare il proprio profilo

**Automobilista:**

1. Effettuare login  
2. Visualizzare dashboard con prenotazioni del annuncio  
3. Gestire appuntamenti propri  
4. Confermare prenotazione   
5. Rifiutare prenotazione  
6. Visualizzare dettagli passeggero per annuncio  
7. Modificare il proprio profilo

**Prenotazione:**

**Annuncio:**

1. [Sistema] Generare notifiche di conferma  
2. [Sistema] Validare che non ci siano sovrapposizioni  
3. [Sistema] Verificare disponibilità dei posti in macchina 

---

### 2.2 Flussi di Business

**FLUSSO 1: Prenotazione Annuncio**

1. Passeggero effettua login

2. Passeggero visualizza lista annunci

3. Passeggero seleziona annuncio

4. Passeggero visualizza disponibilità del posti in macchina del annuncio

5. Passeggero prenota viaggio

6. Sistema verifica disponibilità dell viaggio

7. Sistema crea prenotazione(proposta, decrementa disponibilita posti)

8. Sistema invia notifica di proposta di prenotazione

9. Sistema riceve accettazione/rifiuto della proposta

10. Sistema conferma/anulla la prenotazione (se anullata invia la notifica a passegero ee incrementa disponibilita)

**FLUSSO 2: Gestione Disponibilità Automobilista**

1. Automobilista effettua login

2. Automobilista crea annuncio

3. Annuncio riceve prenotazione per il viaggio

4. Automobilista accetta/rifiuta i prenotazioni

5. Sistema aggiorna disponibilità dei posti in macchina

6. Sistema blocca automaticamente il disponibilita di annunci(se posti sono essauriti)


---

## FASE 3: PROGETTAZIONE DELLE RISORSE REST

### 3.1 Identificazione delle Risorse

**Risorse base:**

- /passeggeri

- /automobilisti

- /notifiche

**Risorse annidate:**

- /automobilisti/{id}/annunci/{id}/disponibilita

- /automobilisti/{id}/annunci

- /passeggeri/{id}/annunci

- passeggeri/{id}/annunci/{id}/notifica

**Risorse speciali:**

- /auth/registrazione

- /auth/login

- /dashboard/annunci

---

### 3.2 Mappatura Operazioni → Metodi HTTP

| Operazione | Metodo HTTP | Endpoint | Accesso |
| :---- | :---- | :---- | :---- |
| Registrazione paziente | POST | `/api/v1/auth/registrazione` | Pubblico |
| Login | POST | `/api/v1/auth/login` | Pubblico |
| Logout | POST | `/api/v1/auth/logout` | Autenticato |
| Lista annunci | GET | `/api/v1/annunci` | Pubblico/Autenticato |
| Dettaglio annuncio | GET | `/api/v1/annunci/{id}` | Pubblico/Autenticato |
| Disponibilità annuncio | GET | `/api/v1/annunci/{id}/disponibilita` | Autenticato |
| Prenota annuncio | POST | `/api/v1/annuncio` | Passeggero |
| Lista appuntamenti passeggero | GET | `/api/v1/passeggeri/{id}/annunci` | Passeggeri (propri) |
| Cancella annuncio | DELETE | `/api/v1/annunci/{id}` | Passeggeri (propri) |
| Lista prenotazzioni annuncio | GET | `/api/v1/annunci/{id}/prenotazioni` | Automobilista |
| Gestisci disponibilità annuncio | POST/PUT | `/api/v1/annuncio/{id}/disponibilita` | Automobilista |
| Conferma prenotazione | PATCH | `/api/v1/annuncio/{id}/conferma` | Automobilista |
| Rifiuta prenotazione | PATCH | `/api/v1/annuncio/{id}/rifiuta` | Automobilista |

---

## FASE 4: DEFINIZIONE DEI DTO

### 4.1 Esempi Request Body

**POST /api/v1/auth/registrazione**

{

  "nome": "Mario",

  "cognome": "Rossi",

  "email": "mario.rossi@example.com",

  "telefono": "+39 123 456 7890",

  "codice_fiscale": "RSSMRA80A01H501U",

  "data_nascita": "1980-01-01",

  "password": "passwordSicura123!",

  "ruolo": "paziente"

}

**POST /api/v1/appuntamenti**

{

  "medico_id": 5,

  "data": "2025-02-15",

  "ora": "14:30",

  "motivo_visita": "Controllo annuale",

  "durata_minuti": 30

}

**POST /api/v1/medici/{id}/disponibilita**

{

  "giorno_settimana": "lunedi",

  "orario_inizio": "09:00",

  "orario_fine": "13:00",

  "attivo": true

}

---

### 4.2 Esempi Response Body

**GET /api/v1/medici/{id}**

{

  "id": 5,

  "nome": "Dr. Giovanni",

  "cognome": "Bianchi",

  "email": "g.bianchi@studio.it",

  "specializzazione": "Cardiologia",

  "numero_albo": "12345",

  "studio": {

    "id": 2,

    "nome": "Studio Medico Centro",

    "indirizzo": "Via Roma 10, Milano"

  },

  "links": {

    "self": "/api/v1/medici/5",

    "disponibilita": "/api/v1/medici/5/disponibilita",

    "appuntamenti": "/api/v1/medici/5/appuntamenti"

  }

}

**GET /api/v1/medici/{id}/disponibilita?data=2025-02-15**

{

  "medico": {

    "id": 5,

    "nome": "Dr. Giovanni",

    "cognome": "Bianchi"

  },

  "data": "2025-02-15",

  "orari_disponibili": [

    {

      "ora": "09:00",

      "disponibile": true

    },

    {

      "ora": "09:30",

      "disponibile": false,

      "motivo": "già prenotato"

    },

    {

      "ora": "10:00",

      "disponibile": true

    }

    // ... altri orari

  ]

}

**GET /api/v1/dashboard/medico**

{

  "medico": {

    "id": 5,

    "nome": "Dr. Giovanni",

    "cognome": "Bianchi"

  },

  "data_odierna": "2025-01-15",

  "appuntamenti_oggi": [

    {

      "id": 123,

      "ora": "10:00",

      "paziente": {

        "id": 10,

        "nome": "Mario",

        "cognome": "Rossi"

      },

      "motivo_visita": "Controllo",

      "stato": "confermato"

    }

  ],

  "appuntamenti_settimana": 12,

  "appuntamenti_in_attesa": 3

}

---

## FASE 5: GESTIONE ERRORI

**Esempio risposta errore:**

{

  "error": {

    "code": "SLOT_NON_DISPONIBILE",

    "message": "L'orario selezionato non è più disponibile",

    "details": {

      "medico_id": 5,

      "data": "2025-02-15",

      "ora": "09:30",

      "alternativa_suggerita": {

        "ora": "10:00",

        "disponibile": true

      }

    },

    "timestamp": "2025-01-15T14:30:00Z"

  }

}

---

## FASE 6: AUTENTICAZIONE

**POST /api/v1/auth/login**

Request:

{

  "email": "mario.rossi@example.com",

  "password": "passwordSicura123!"

}

Response (200 OK):

{

  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",

  "expires_in": 3600,

  "user": {

    "id": 10,

    "nome": "Mario",

    "cognome": "Rossi",

    "email": "mario.rossi@example.com",

    "ruolo": "paziente"

  }

}

---

## CHECKLIST FINALE

Per questo progetto, verificate di aver definito:

### ✅ Analisi

- [ ] Tutte le entità principali sono state identificate  
- [ ] Le relazioni tra entità sono state mappate  
- [ ] I ruoli utente sono stati definiti con permessi chiari

### ✅ Progettazione API

- [ ] Tutte le operazioni sono mappate a endpoint REST  
- [ ] I metodi HTTP sono corretti per ogni operazione  
- [ ] Gli endpoint seguono convenzioni REST (plurali, gerarchici)  
- [ ] I parametri query sono definiti per filtri e ricerca

### ✅ Dati

- [ ] Le strutture Request body sono definite per ogni POST/PUT/PATCH  
- [ ] Le strutture Response body sono definite per ogni endpoint  
- [ ] Le validazioni sui dati sono specificate  
- [ ] I codici di stato HTTP sono appropriati

### ✅ Sicurezza

- [ ] L'autenticazione è richiesta dove necessario  
- [ ] Le autorizzazioni per ruolo sono definite  
- [ ] Gli endpoint pubblici vs privati sono identificati

### ✅ Funzionalità Speciali

- [ ] Le notifiche sono progettate  
- [ ] La ricerca e i filtri sono implementabili  
- [ ] Le relazioni tra risorse sono rappresentate (link o embedded)  
- [ ] La paginazione è prevista per liste lunghe

---

**Buon lavoro nella definizione delle vostre API REST! 🚀**  
