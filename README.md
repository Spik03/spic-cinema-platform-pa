# 🎬 SPIC — Showcase Platform International Cinema

Applicazione desktop distribuita con architettura **Client-Server** sviluppata in **Java**, composta da un'interfaccia grafica **JavaFX** (Client) e da un servizio **RESTful Spring Boot** (Server) con persistenza su **MySQL** e popolamento automatico tramite le API di **The Movie Database (TMDB)**.

Sviluppato per il corso di **Programmazione Avanzata** presso l'**Università di Pisa** (Corso di Laurea Triennale in Ingegneria Informatica, A.A. 2024/2025).

---

## 📌 Panoramica

**SPIC** consente agli utenti di esplorare un catalogo di film e serie TV e di organizzare le proprie liste personali in modo indipendente:

* **Autenticazione & Sicurezza:** Registrazione e login con cifratura delle password lato server e gestione delle sessioni utente.
* **Inizializzazione Automatica del Catalogo (`TMDB API`):** Al primo avvio, il server crea lo schema su MySQL e popola le tabelle `film` e `serietv` interrogando le API di TMDB, applicando filtri sui contenuti (`include_adult=false` e validazione sui caratteri latini).
* **Gestione Liste Indipendenti:** Ogni prodotto può essere aggiunto o rimosso in modo indipendente tra **Watchlist**, **Watched** e **Preferiti**.
* **Ricerca & Filtri Combinati:** Filtro istantaneo per titolo, switch tra **Film** e **Serie TV**, e filtri combinabili per stato (*Tutti*, *In Lista*, *Visti*, *Preferiti*).
* **Anteprima Rapida & Dettagli:** Tooltip dinamico con la locandina al passaggio del mouse sul titolo e finestra di dettaglio (doppio click) con trama, voto medio, numero di voti e classificazione.

---

## 🏗️ Architettura del Sistema

Il sistema separa nettamente la presentazione client-side dalla logica di accesso ai dati:

* **Client (`JavaFX` + `Maven`):**
  * Interfacce dichiarative in **FXML** (`login.fxml`, `main.fxml`, `descrizione.fxml`) con foglio di stile custom (`style.css`) e centraggio dinamico delle finestre.
  * Comunicazione asincrona multi-thread verso il server tramite classi dedicate (`JsonCreator` e `DatabaseCreator` che estendono `Thread`) e parsing JSON tramite **Gson / Jackson**.
* **Server (`Spring Boot` + `Spring Data JPA` + `MySQL`):**
  * Espone endpoint REST (`GET/POST/PUT`) per verifica/creazione DB (`/DB/test`, `/DB/create`), autenticazione (`/utente/login`, `/utente/register`) e gestione liste (`/listafilm/*`, `/listaserietv/*`).
  * Utilizzo del pattern **Data Transfer Object (`ProdottoDTO`)** per mappare i risultati delle `LEFT JOIN` tra catalogo e preferenze utente senza esporre direttamente le entità JPA.
  * Suite di test automatizzati con **JUnit / Spring Boot Test** (`ApiTest`, `UtenteTest`, `ServerApplicationTests`).

---

## 🗂️ Struttura della Repository

```text
spic-cinema-platform-pa/
├── client/                         # Applicazione desktop JavaFX (Maven)
│   ├── pom.xml
│   └── src/main/
│       ├── java/it/unipi/spicuzza/client/      # Controller FXML, Thread HTTP/JSON, DTO
│       └── resources/                          # Viste FXML, CSS, icone e config.properties
├── server/                         # Microservizio REST Spring Boot (Maven)
│   ├── pom.xml
│   └── src/
│       ├── main/java/it/unipi/spicuzza/server/ # Controller REST, Service, Repository, Entity, DTO
│       ├── main/resources/                     # application.properties e config.properties
│       └── test/java/it/unipi/spicuzza/server/ # Suite di test (ApiTest, UtenteTest)
├── docs/
│   ├── Documentazione.pdf          # Relazione tecnica completa
│   └── specifiche.pdf              # Specifiche ufficiali del progetto
├── .gitignore
└── README.md
```

---

## 🚀 Installazione e Avvio

### 1. Prerequisiti
* **Java JDK 17+** (o versione configurata nel `pom.xml`)
* **Apache Maven** (oppure usa il wrapper `./mvnw` incluso in `server/`)
* **MySQL Server** attivo in locale (`127.0.0.1:3306`)

### 2. Configurazione API Key & Backend (`server/`)
> **Nota importante:** Per il corretto funzionamento dell'inizializzazione del catalogo, è necessario richiedere una **API Key gratuita da TMDB** (registrandosi su [themoviedb.org](https://www.themoviedb.org/settings/api)) e inserirla nel file `server/src/main/resources/config.properties`.

1. Verifica i parametri di connessione MySQL in `server/src/main/resources/application.properties` (di default utente `root`, password `root` — il database viene creato automaticamente da Spring).
2. Inserisci la tua chiave API di TMDB in `server/src/main/resources/config.properties`.
3. Avvia il server Spring Boot:
   ```bash
   cd server
   ./mvnw spring-boot:run
   ```

### 3. Avvio Client (`client/`)
1. Apri un secondo terminale e avvia l'applicazione JavaFX tramite Maven:
   ```bash
   cd client
   mvn clean javafx:run
   ```
2. Nella schermata di Login, clicca su **"Popola Database"** al primo avvio per scaricare film e serie TV da TMDB, quindi registra un nuovo utente ed effettua l'accesso.

---

## 📄 Documentazione

La relazione completa è consultabile in:

* 📘 **[Documentazione Completa di Progetto (PDF)](docs/Documentazione.pdf)**
* 📋 **[Specifiche del Progetto d'Esame (PDF)](docs/specifiche.pdf)**