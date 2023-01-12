lezione 13

# contenitori sequenziali:
La libreria standard di C++ fornisce una serie di contenitori di sequenza che possono essere utilizzati per gestire una serie di elementi di un tipo di dati specifico in modo efficiente. Ecco una lista dei principali contenitori di sequenza forniti dalla libreria standard di C++:

-   `array`: è un contenitore di sequenza statico che gestisce una serie di elementi di un tipo di dati specifico in modo efficiente. `array` fornisce funzionalità come l'accesso agli elementi individuali, la modifica degli elementi e l'iterazione attraverso gli elementi.
    
-   `vector`: è un contenitore di sequenza dinamico che gestisce una serie di elementi di un tipo di dati specifico in modo efficiente. `vector` fornisce funzionalità come l'aggiunta, l'eliminazione e l'accesso a elementi individuali in modo efficiente.
    
-   `forward_list`: è un contenitore di sequenza dinamico che gestisce una serie di elementi di un tipo di dati specifico in modo efficiente. `forward_list` fornisce funzionalità come l'inserimento, la rimozione e l'accesso a elementi individuali in modo efficiente, ma solo in avanti nella sequenza.
    
-   `deque`: è un contenitore di sequenza dinamico che gestisce una serie di elementi di un tipo di dati specifico in modo efficiente. `deque` fornisce funzionalità come l'inserimento, la rimozione e l'accesso a elementi individuali in modo efficiente sia all'inizio che alla fine della sequenza.

queste funzioni non permettono l'ordinamento dei elementi, possiamo usare dei algoritmi generici,  per accedere agli elementi e il valore delle loro posizioni possiamo usare una copia di iteratori:

   [first, last)

dove la parentesi quadra iniziale ci informa che l'elemento indicato da first è compreso nella sequenza, mentre la parentesi tonda finale ci informa che l'elemento indicato da last è escluso dalla sequenza.

# ITERATORI:

Gli iteratori sono una caratteristica della programmazione ad oggetti che consentono di accedere agli elementi di una sequenza di dati in modo efficiente. Gli iteratori sono simili ai puntatori, ma sono progettati per essere utilizzati con classi di contenitori come ad esempio `vector` o `list`.

Gli iteratori forniscono funzionalità come l'accesso agli elementi della sequenza, la modifica degli elementi e il spostamento all'interno della sequenza. Inoltre, gli iteratori sono generici, il che significa che possono essere utilizzati con qualsiasi tipo di dati.

Ecco come puoi usare gli iteratori in C++:
```
#include <vector>

int main() {
  // Crea un vector di interi con 5 elementi
  std::vector<int> v {1, 2, 3, 4, 5};

  // Crea un iteratore che punta all'inizio del vector
  std::vector<int>::iterator it = v.begin();

  // Avanza l'iteratore di 2 posizioni
  it += 2;

  // Modifica l'elemento puntato dall'iteratore
  *it = 42;

  // Stampa l'elemento puntato dall'iteratore
  std::cout << *it << std::endl; // stamperà 42

  return 0;
}
```

Dato che usare i puntatori e T per un algoritmo generico per l'ordinamento dei elementi dobbiamo trovare un nuovo tipo più "applicabile". Quindi sostituiamo il tipo T* con un ulteriore parametro di template che deve fornire il concetto di iteratore (non necessariamente un puntatore):
```
template <typename Iter, typename T>
Iter cerca(Iter first, Iter last, T elem) {
  for ( ; first != last; ++first)
    if (*first == elem)
      return first;
  return last;
}
```
Quali sono i requisiti per potere istanziare correttamente il tipo Iter nell'algoritmo generico qui sopra?

1) Iter deve supportare la copia (passato e restituito per valore)
2) Iter deve supportare il confronto binario (first != last), per capire se la sequenza è terminata o meno
3) Iter deve supportare il preincremento (++first), per avanzare di una posizione nella sequenza
4) Iter deve consentire la dereferenziazione (*first), per poter leggere il valore "puntato"
5) il tipo dei valori puntati da Iter deve essere confrontabile con il tipo T (usando l'operatore == )


* I contenitori associativi

I contenitori associativi sono contenitori che organizzano gli elementi al loro interno in modo da facilitarne la ricerca in base al valore di una "chiave".


Molti algoritmi generici della libreria standard lavorano sul concetto di sequenza. Il concetto di iteratore, che prende spunto dal puntatore, fornisce un modo efficace per rappresentare varie tipologie di sequenze, indipendentemente dal tipo concreto usato per la loro implementazione.

Gli iteratori si possono classificare in 5 categorie distinte (che corrispondono, tecnicamente, a 5 concetti correlati ma distinti), che si differenziano per le operazioni supportate e per le corrispondenti garanzie fornite all'utente. Le categorie sono:

  * iteratori di input
  * iteratori forward
  * iteratori bidirezionali
  * iteratori random access
  * iteratori di output