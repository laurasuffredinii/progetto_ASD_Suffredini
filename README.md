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
        *Per mappare solo i nodi effettivamente toccati dai cammini e mappare i loro identificatori sparsi (numeri a 32 bit) in indici contigui compatti $[0, \vert{}V\vert{}-1]$, evitando sprechi di memoria.
        *Per contare le frequenze degli archi durante la lettura dei cammini.
    * Vettori: 
        * Per la mappatura inversa da indici contigui compatti a identificatori AS originali.
    * Liste di adiacenza: 
        * Per rappresentare il grafo pesato e non orientato, con archi etichettati dai pesi (frequenze di attraversamento).
    * Coda/Stack: 
        * Per eseguire la visita (DFS o BFS) in modo da trovare la componente connessa principale del grafo.

### 2. Modulo di Ricerca Cammini Minimax (MinimaxSolver)
*   Obiettivo: Fornire una struttura dati e un algoritmo efficiente per rispondere alle query sul costo del cammino minimax ottimo tra coppie di nodi.. 
*   Compiti: 
    *Costruire una struttura dati che consenta di rispondere alle query sul costo del cammino minimax ottimo tra due nodi $u$ e $v$.
    * Determinare il costo del cammino minimax ottimo tra due nodi $u$ e $v$, individuando il valore che minimizza la massima frequenza tra gli archi attraversati lungo il percorso.
* Strrutture dati utilizzate:  
    * Liste/Vettori: 
        *


### 3. Modulo di Analisi Sperimentale (ExperimentalAnalyzer)

* Obiettivo: Analizzare la struttura del grafo ricavato dai dati e misurare i tempi effettivi di esecuzione delle varie parti dell'algoritmo.
* Compiti:
    * Calcolare il numero di nodi e archi.
    * Generare un grafico che mostri la distribuzio e dei pesi tra tutti gli archi del grafo.
    * Cronometrare i tempi di esecuzione delle singole fasi.
    * Visualizzare e salvare i risultati ottenuti.
* Strutture dati utilizzate: