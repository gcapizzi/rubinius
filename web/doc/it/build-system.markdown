---
layout: doc_it
title: Sistema di build
previous: Specifiche del compilatore
previous_url: specs/compiler
next: Bootstrapping
next_url: bootstrapping
review: true
---

TODO: Mancano molti dettagli.

Rubinius utilizza Rake come sistema di build per i suoi file. Ciò nonostante,
Rubinius include anche i sorgenti di molte librerie esterne, che solitamente
utilizzano i Makefile.

## Build di sviluppo e build di installazione

Rubinius è costituito da un eseguibile e da vari file di supporto, quali ad
esempio la core library e la standard library. L'eseguibile necessita di
sapere dove trovare questi file, indipendentemente da dove esso si trovi. Per
risolvere questo problema, Rubinius distingue due tipi di build: sviluppo
e installazione. L'eseguibile memorizza il percorso completo della sua
cartella di base, dal quale esso calcola la posizione dei file di cui ha
bisogno.

L'eseguibile di sviluppo memorizza il percorso della cartella dei sorgenti in
cui è stato compilato. È possibile spostare l'eseguibile in un'altra cartella,
ma esso continuerà ad utilizzare i file della core library contenuti nelle
cartelle kernel/\*\*.

L'eseguibile di installazione memorizza invece il percorso della cartella di
installazione. Anche in questo caso, anche se spostato altrove, l'eseguibile
sarà in grado di localizzare i file installati.

Ciò ha, ovviamente, delle conseguenze. Se viene effettuata una build di
sviluppo e successivamente la cartella dei sorgenti viene rinominata, sarà
necessario compilare nuovamente. Allo stesso modo, se viene effettuata una
build di installazione e viene successivamente rinominata la cartella di
installazione, l'eseguibile *non* funzionerà, *anche se si trova nella
cartella stessa*. L'eseguibile controlla un singolo percorso, cablato al suo
interno. Se questa pratica si rivelerà particolarmente dolorosa per qualche
motivo, la cambieremo.

## Installare Rubinius

Per installare Rubinius, dovete prima configurarlo specificando un prefisso di
installazione:

    ./configure --prefix=/path/to/install/dir

Il processo di configurazione crea un file 'config.rb' che specifica
i percorsi ai file utilizzati da Rubinius. Una volta effettuata la
configurazione, lanciate il comando 'rake install' per eseguire la build di
installazione. La procedura di installazione compila tutti i file, compresi
i file dalla standard library contenuti nella cartella lib/, e successivamente
li copia nella loro destinazione finale utilizzando il programma 'install'.
I task relativi all'installazione sono contenuti nel file
rakelib/install.rake.
