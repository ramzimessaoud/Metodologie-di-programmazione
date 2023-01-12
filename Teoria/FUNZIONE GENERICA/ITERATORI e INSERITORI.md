




INSERITORI

3 tipi:

std::back_insert_iterator inserisce sempre infondo

std::insert_front_iterator inserisce sempre davanti (non funziona con vector)

std::Insert_iterator inserisce nella posizione che li forniamo.

devo includere 
````
#include <iterator> 
#include <iostream>
````

possiamo usare la deduzione automatica con 

std::back_inserter
std::front_inserter
std::inserter

per risparmiare di scrivere tutto il tipo.

Gli inseritori sono oggetti funzionali definiti nella libreria standard di C++ che consentono di inserire elementi in un container. Gli inseritori sono implementati come iteratori di output e sono basati sul modello di iteratore inseritore di output (std::insert_iterator). Gli inseritori vengono utilizzati per inserire elementi in un container in modo semplice e rapido, senza dover scrivere codice specifico per ogni tipo di container.

La libreria standard fornisce tre specializzazioni degli inseritori: std::back_inserter, std::front_inserter e std::inserter.

-   std::back_inserter è un iteratore di output che consente di inserire elementi in un container di tipo sequenza alla fine della sequenza.
-   std::front_inserter è un iteratore di output che consente di inserire elementi in un container di tipo sequenza all'inizio della sequenza.
-   std::inserter è un iteratore di output che consente di inserire elementi in un container di tipo associativo (ad esempio, un std::map) in base all'ordine di chiave.