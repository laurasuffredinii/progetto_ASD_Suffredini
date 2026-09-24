# progetto_ASD_Suffredini

## Obiettivo del Progetto
Il progetto ha lo scopo di analizzare la topologia di rete degli Autonomous Systems (AS) a partire da statistiche BGP reali, costruendo un grafo pesato per individuare il costo per cammini minimax ottimi. 

## Architettura Iniziale e Moduli Principali

Il software è stato suddiviso nei seguenti moduli principali:

### 1. Modulo di Costruzione del Grafo (GraphBuilder)
* Questo modulo ha lo scopo di eseguire il parsing dei dataset BGP (file `.all-paths.bz2`) e genera il grafo pesato e non orientato.
* Si mappano gli identificatori AS, si inseriscono i nodi/archi, e si calcolano le frequenze degli archi, che corrispondono ai pesi.
* Strutture dati utilizzate:

### 2. Modulo di Ricerca Cammini Minimax (MinimaxSolver)
*   Tale modulo ha il compito di costruire una struttura dati per rispondere alle query sul costo del cammino minimax ottimo. 
*   Si determina il costo del cammino minimax ottimo tra due nodi $u$ e $v$, individuando il valore che minimizza la massima frequenza tra gli archi attraversati lungo il percorso.

### 3. Modulo di Analisi Sperimentale (ExperimentalAnalizer)
Questo modulo ha lo scopo di raccogliere il numero di nodi, gli archi, la distribuzione dei pesi e misurare i tempi di esecuzione degli algoritmi implementati.

