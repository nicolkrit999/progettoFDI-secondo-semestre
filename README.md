# Documentazione e Struttura del Progetto

## 🛠 Requisiti di Sistema

Il progetto è configurato per utilizzare **Java Development Kit (JDK) 25**.

**Nota sulle versioni del JDK:** Tutti i contributori devono avere installata una versione di JDK 25, ma non è importante quale distribuzione specifica venga utilizzata (Oracle, Eclipse Temurin, OpenJDK, ecc.). Poiché Maven gestisce il processo di compilazione localmente su ogni macchina (e lo standardizza tramite il file `pom.xml`), leggere differenze tra le distribuzioni non influiranno negativamente sul progetto. I file compilati sono specifici per l'ambiente in cui vengono generati, quindi l'importante è rispettare la versione del linguaggio (Java 25).

## 📁 Struttura delle Cartelle

Il progetto segue le convenzioni standard di Maven, posizionate all'interno della cartella `src/`.

Di seguito la spiegazione di dove si trovano i file e, soprattutto, **dove inserire i nuovi file**:

* **`documentation/`**: Questa cartella contiene i file di spiegazione del progetto (come questo) e i log dei report generati automaticamente dalle GitHub Actions.
    * **`do not move or rename`** `github-actions-reports`
* **`src/`**: La directory principale che contiene tutto il codice e la configurazione di build.
* **`pom.xml`**: Il file di configurazione di Maven. Gestisce le dipendenze e le impostazioni di compilazione. Se mancante, verrà autogenerato dalla pipeline (github action).
* **`main/java/`**: **Qui va inserito tutto il codice sorgente (logica del progetto).**
* `Main.java`: Il punto di ingresso principale dell'applicazione (l'avvio generico).
* `simulation/SimulationRunner.java`: La classe deputata a inizializzare ed eseguire esclusivamente la logica di simulazione del progetto. Se si creano nuove classi per la simulazione o per altri moduli, andranno inserite qui, organizzate in package coerenti (es. `src/main/java/models/`, `src/main/java/controllers/`, ecc.).


* **`test/java/`**: **Qui vanno inseriti tutti i test automatizzati (es. JUnit).**
* `simulation/SimulationRunnerTest.java`: Un test di base per verificare che l'ambiente di testing sia configurato e funzionante. Quando si scrivono nuovi test, la struttura dei package qui dentro deve rispecchiare esattamente quella di `main/java/`.





### Dove si trovano i file compilati (`.class`)?

Quando il codice viene compilato (eseguendo Maven manualmente o tramite l'azione automatica), verrà generata automaticamente una nuova cartella chiamata **`src/target/`**.

Questa cartella **non esiste nel repository online** e non deve mai essere caricata. Contiene i file `.class` (il bytecode Java pronto per essere eseguito) e i report temporanei generati da Maven. Git ignorerà questa cartella in automatico.

---

## 🤖 Automazione: La GitHub Action

Per facilitare lo sviluppo e mantenere il codice pulito, il repository è dotato di un sistema di Continuous Integration (CI) tramite GitHub Actions. Questo sistema agisce come un "assistente" che non blocca il lavoro, ma lo rifinisce e lo documenta.

### Quando si attiva?

L'azione parte in due casi:

1. **In automatico (On Push):** Ogni volta che qualcuno carica (push) del nuovo codice sul branch `develop`.
2. **Manualmente:** Andando nella tab "Actions" di GitHub e cliccando su "Run workflow".

### Cosa fa esattamente?

Una volta avviata, la Action esegue i seguenti passaggi in un ambiente isolato (Ubuntu + JDK 25):

1. **Controllo Setup:** Verifica la presenza del file `pom.xml`. Se non c'è, ne genera uno di base in automatico.
2. **Formattazione:** Passa in rassegna tutti i file Markdown (`.md`) e i file Java (`.java`), formattandoli secondo gli standard (tramite Prettier e Google Java Format). Se c'è un errore di sintassi, il file viene ignorato senza far fallire il processo.
3. **Compilazione (Best Effort):** Tenta di compilare il codice. Se un file ha errori che impediscono la creazione del `.class`, viene semplicemente ignorato e si procede.
4. **Esecuzione Test:** Cerca la cartella `src/test/java/`. Se esiste, esegue i test e ne salva il risultato. Se non esiste o manca qualche file, lo annota senza causare errori.
5. **Esecuzione Simulazione:** Cerca la classe `SimulationRunner.java` e prova ad avviarla. Anche qui, cattura l'output generato dalla console.

### Qual è il risultato finale?

La GitHub Action **non fa commit diretti** sul tuo branch. Invece, raccoglie tutto il lavoro fatto (formattazioni e report) e **crea una singola Pull Request** automatica.

All'interno di questa Pull Request troverai:

* I tuoi file `.java` e `.md` ben formattati.
* Una nuova sotto-cartella dentro `documentation/github-actions-reports/`, nominata con la **Data e l'Ora** dell'esecuzione.
* Dentro questa cartella, due file Markdown:
* `test-results.md`: L'esito dei test eseguiti.
* `simulation-results.md`: L'output testuale della tua simulazione.


* Verrà inoltre aggiornato un file indice (`README.md` nei report) per mantenere la cronologia ordinata di tutte le simulazioni e i test effettuati nel tempo.

