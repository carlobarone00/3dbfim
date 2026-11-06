In questo notebook sono presenti 9 celle. Gli output dovrebbero essere già visibili; qualora così non fosse, le basta impostare il runtime su GPU T4 (Runtime -> Cambia tipo di runtime) e poi eseguire Runtime -> Esegui tutto. Con il FMM l'esecuzione completa richiede diversi minuti.

Le 9 celle sono:
ambiente.h: descrive l'ambiente (stanza, muri, porte e finestre, punti A e B) e costruisce la maschera dei nodi negli ostacoli, gonfiati di un margine di sicurezza;

godunov3d.h: risolutore locale di Godunov al primo ordine, comune ai tre metodi, e alcune costanti (per esempio INF);

fmm3d.h: Fast Marching Method sequenziale su CPU, usato come riferimento;
block_fim3d.cuh: il cuore del codice, il Block FIM su GPU (blocchi di 8 x 8 x 8 nodi) con i kernel CUDA;

jacobi_fim.cuh: FIM-Jacobi su GPU, che aggiorna tutti i nodi a ogni iterazione;

percorso.h: calcola il percorso da A a B seguendo -grad u con Runge-Kutta del 4 ordine e simula il volo con le porte che si chiudono;

main.cu: la funzione fm2 esegue il Fast Marching Square in due passate. Nella prima le sorgenti sono i nodi liberi a contatto con gli ostacoli: si calcola la distanza d di ogni nodo dagli ostacoli e da questa la lentezza F (alta vicino ai muri, 1 lontano). Nella seconda si calcola u, il tempo di arrivo da B, tenendo conto di F. Il main stampa una tabella di confronto tra i tre metodi al variare di N e poi la ripianificazione: per ogni chiusura di porta si ricalcola il FM2 e si misura il tempo di ciascun metodo;

!nvcc: compila ed esegue il tutto, l'output contiene le due tabelle;

cella Python: legge i voli calcolati e mostra l'animazione 3D dei tre droni (uno per metodo) e il grafico delle velocità.
