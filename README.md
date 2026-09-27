# progetto_ASD_Suffredini

## Obiettivo del Progetto
Il progetto ha lo scopo di analizzare la topologia di rete degli Autonomous Systems (AS) a partire da statistiche BGP reali, costruendo un grafo pesato per individuare il costo per cammini minimax ottimi. 

## Architettura Iniziale e Moduli Principali

Il software è stato suddiviso nei seguenti moduli principali:

### 1. Modulo di Costruzione del Grafo (GraphBuilder)
* Obiettivo: Generare il grafo pesato e non orientato a partire dai dataset BGP, isolandone la componente connessa più grande.

* Compiti:
    * Eseguire il parsing dei file .all-paths.bz2.
    * Mappare gli identificatori AS (numeri a 32 bit) in indici contigui compatti.
    * Inserire nodi e archi, calcolando le frequenze di attraversamento (che corrispondono ai pesi).
    * Identificare ed estrarre la componente connessa principale.

* Strutture dati utilizzate:
    * Tabelle hash: 
        * Per mappare solo i nodi effettivamente toccati dai cammini e mappare i loro identificatori sparsi (numeri a 32 bit) in indici contigui compatti $[0, \vert{}V\vert{}-1]$, evitando sprechi di memoria.
        * Per contare le frequenze degli archi durante la lettura dei cammini.
    * Vettori: 
        * Per la mappatura inversa da indici contigui compatti a identificatori AS originali.
    * Liste di adiacenza: 
        * Per rappresentare il grafo pesato e non orientato, con archi etichettati dai pesi (frequenze di attraversamento).
    * Coda/Stack: 
        * Per eseguire la visita (DFS o BFS) in modo da trovare la componente connessa principale del grafo.

### 2. Modulo di Ricerca Cammini Minimax (MinimaxSolver)
*   Obiettivo: Fornire una struttura dati e un algoritmo efficiente per rispondere alle query sul costo del cammino minimax ottimo tra coppie di nodi.
*   Compiti: 
    * Costruire una struttura dati che consenta di rispondere alle query sul costo del cammino minimax ottimo tra due nodi $u$ e $v$.
    * Determinare il costo del cammino minimax ottimo tra due nodi $u$ e $v$, individuando il valore che minimizza la massima frequenza tra gli archi attraversati lungo il percorso.
* Strutture dati utilizzate:  
    * Union-Find (Disjoint Set Union - DSU):
        * Per gestire le componenti connesse provvisorie e rilevare i cicli in tempo quasi-costante durante la selezione degli archi con Kruskal.
    * Vettori:
        * Per la struttura interna dell'Union-Find (vettore dei padri/rappresentanti e delle taglie/ranghi)
        * Per memorizzare la sequenza degli archi del grafo ordinata per peso.
    * Liste di adiacenza (albero MST):
        * Per memorizzare il sottografo ad albero risultante ($|V|-1$ archi), permettendo visite rapide (DFS/BFS) per rispondere alle query sui cammini.

### 3. [Modulo Opzionale] Conteggio cammini minimax ottimi (predisposto per raffinamenti futuri).

### 4. Modulo di Analisi Sperimentale (ExperimentalAnalyzer)
* Obiettivo: Analizzare la struttura del grafo ricavato dai dati e misurare i tempi effettivi di esecuzione delle varie parti dell'algoritmo.
* Compiti:
    * Calcolare il numero di nodi e archi della componente connessa principale.
    * Generare un grafico che mostri la distribuzione dei pesi tra tutti gli archi del grafo.
    * Riportare il costo ottimo di un cammino minimax per coppie di nodi scelte.
    * Cronometrare i tempi di esecuzione delle singole fasi.
    * Visualizzare e salvare i risultati ottenuti.
* Strutture dati utilizzate:
    * Vettori:
        * Per memorizzare i pesi degli archi e calcolare la distribuzione.
    * Liste di adiacenza:
        * Per rappresentare il grafo durante l'analisi.
    * Strutture per la visualizzazione dei dati:
        * Per generare grafici e report dei risultati.


## Interazioni tra i Moduli e Flusso dei Dati

L'architettura del software prevede che i moduli interagiscano tra loro in modo sequenziale:
### 1. Da GraphBuilder a MinimaxSolver
* **Dati scambiati:** Il grafo della sola componente connessa principale, rappresentato tramite la lista di adiacenza con vertici rinumerati nell'intervallo compatto $[0, \vert{}V_{LCC}\vert{} - 1]$.
* **Dinamica:** `GraphBuilder` conclude la fase di pulizia, scarta i nodi isolati ed estrae la componente connessa più grande. Consegna questa struttura a `MinimaxSolver`, che può così applicare l'ordinamento degli archi e l'algoritmo di Kruskal senza il rischio di trovare partizioni disconnesse.

### 2. Da `GraphBuilder` a `MinimaxSolver`
* **Dati scambiati:** Il vettore completo dei pesi degli archi e le metriche topologiche aggregate (numero totale di nodi e archi del grafo originale a confronto con quelli della componente connessa principale).
* **Dinamica:** `GraphBuilder` mette a disposizione di `ExperimentalAnalyzer` i dati quantitativi estratti, permettendo al modulo di calcolare le statistiche strutturali e generare il grafico di distribuzione delle frequenze.


### 3. Da MinimaxSolver a ExperimentalAnalyzer
* **Dati scambiati:** I costi minimax ottimi per le coppie di nodi interrogate e le metriche prestazionali.
* **Dinamica:** `MinimaxSolver` riceve le coppie $(u, v)$ selezionate da `ExperimentalAnalyzer` e gli restituisce i rispettivi costi minimax ottimi insieme alle metriche prestazionali, consentendo la stesura dei report e delle tabelle di benchmark.