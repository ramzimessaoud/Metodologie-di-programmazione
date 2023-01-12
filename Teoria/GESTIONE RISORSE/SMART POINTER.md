Gli smart pointer sono un tipo di puntatore che rappresenta una risorsa come un oggetto, con l'obiettivo di automatizzare la gestione delle risorse. Ciò significa che gli smart pointer si occupano di allocare e deallocare le risorse in modo trasparente per l'utente, senza che quest'ultimo debba preoccuparsi di chiamare esplicitamente le funzioni di allocazione e deallocazione come `new` e `delete`. Ciò rende il codice più leggibile e meno propenso a errori, poiché non ci sono puntatori non inizializzati o leak di memoria.

Principali ragioni per cui gli smart pointer sono preferibili alla gestione manuale delle risorse:

1.  Evitano gli errori di memoria: gli smart pointer si occupano automaticamente di allocare e deallocare le risorse, eliminando il rischio di leak di memoria o di accesso a memoria non valida.
    
2.  Migliorano la leggibilità del codice: gli smart pointer rendono il codice più leggibile, poiché eliminano la necessità di scrivere codice di gestione delle risorse manualmente.
    
3.  Gestiscono automaticamente le eccezioni: gli smart pointer possono gestire le eccezioni in modo trasparente, assicurando che le risorse vengano deallocate anche in caso di eccezioni.
    
4.  Semplificano il codice: gli smart pointer possono semplificare il codice eliminando la necessità di scrivere codice di gestione delle risorse manualmente.

Ci sono diverse varianti di smart pointer disponibili nella libreria standard di C++, ognuna delle quali si adatta a situazioni specifiche. Ecco alcuni dei principali tipi di smart pointer, insieme a un semplice esempio di codice che li utilizza:

1.  `std::unique_ptr`: questo smart pointer rappresenta la proprietà esclusiva di una risorsa. Una volta che una risorsa viene assegnata a un `std::unique_ptr`, non può essere condivisa con altri oggetti. Una caratteristica degli unique_ptr è il fatto di NON essere *copiabili*, ma di essere (solo) *spostabili*. La copia è impedita in quanto violerebbe il requisito di unicità del gestore della risorsa; lo spostamento è invece consentito, in quanto si trasferisce la proprietà della risorsa al nuovo gestore. Ad esempio:
````
#include <memory>
#include <iostream>

int main() {
  std::unique_ptr<int> p1(new int(42));
  std::unique_ptr<int> p2 = std::move(p1); // p1 non è più proprietario della risorsa
  std::cout << *p2 << std::endl;
  return 0;
}// qui rilascia automaticamente la risorsa
````

In particolare, unique_ptr implementa il concetto di puntatore "owning",ovvero un puntatore che si considera l'unico proprietario della risorsa.
Intuitivamente, allo smart pointer spetta l'onere di fornire una corretta gestione della risorsa (nello specifico, **rilasciarla a lavoro finito**).



2.  `std::shared_ptr`: questo smart pointer rappresenta la proprietà condivisa di una risorsa. Più oggetti possono condividere la proprietà di una risorsa gestita da un `std::shared_ptr`. implementa il concetto di puntatore per il quale la responsabilità della corretta gestione della risorsa è "condivisa": intuitivamente, ogni volta che uno shared_ptr viene *copiato*, l'originale e la copia condividono la responsabilità della gestione della (stessa) risorsa. A livello di implementazione, la copia causa l'incrementato di un contatore del numero di riferimenti alla risorsa (*reference counter*); quando uno shared_ptr viene distrutto, decrementa il reference counter associato alla risorsa e, se si accorge di essere rimasto l'unico shared_ptr ad avervi ancora accesso, ne effettua il rilascio (informalmente, si dice che "l'ultimo chiude la porta").Ad esempio:

````
#include <memory>

void foo() {
  std::shared_ptr<int> pi;
  {
    std::shared_ptr<int> pj(new int(42)); // ref counter = 1
    pi = pj; // condivisione risorsa, ref counter = 2
    ++(*pi)  // uso risorsa condivisa: nuovo valore 43
    ++(*pj)  // uso risorsa condivisa: nuovo valore 44
  } // distruzione pj, ref counter = 1
  ++(*pi)    // uso risorsa condivisa: nuovo valore 45
} // distruzione pj, ref counter = 0, rilascio risorsa
````

### **I template di funzione std::make_shared e std::make_unique**

std::make_shared e std::make_unique sono due funzioni di creazione di smart pointer della libreria standard di C++. Entrambe sono utilizzate per creare un nuovo oggetto e per gestirne l'allocazione e la deallocazione automaticamente utilizzando un smart pointer.

`std::make_shared` crea un nuovo oggetto di tipo T e lo gestisce tramite uno `std::shared_ptr`. Ad esempio:

````
#include <memory>
#include <iostream>

int main() {
  auto p1 = std::make_shared<int>(42); // crea un nuovo oggetto di tipo int e lo gestisce con un std::shared_ptr
  std::cout << *p1 << std::endl;
  return 0;
}
````
std::make_unique è simile a `std::make_shared`, ma crea un nuovo oggetto di tipo T e lo gestisce tramite un `std::unique_ptr`. Ad esempio:

````
#include <memory>
#include <iostream>

int main() {
  auto p1 = std::make_unique<int>(42); // crea un nuovo oggetto di tipo int e lo gestisce con un std::unique_ptr
  std::cout << *p1 << std::endl;
  return 0;
}
````

Entrambe le funzioni sono particolarmente utili perché **semplificano la creazione di nuovi oggetti e la gestione delle risorse ad essi associate**. Inoltre, poiché utilizzano smart pointer per gestire le risorse, sono più sicure e affidabili rispetto ad altre soluzioni di gestione delle risorse.


3.  `std::weak_ptr`:

Un problema che si potrebbe presentare quando si usano gli shared_ptr (più in generale, quando si usa qualunque meccanismo di condivisione di risorse basato sui reference counter) è dato dalla possibilità di creare insiemi di risorse che, puntandosi reciprocamente tramite
shared_ptr, formano una o più strutture cicliche.
In questo caso, le risorse comprese in un ciclo mantengono dei reference count positivi anche se non sono più raggiungibili a partire dagli shared_ptr ancora accessibili da parte del programma, causando dei memory leak. L'uso dei weak_ptr è pensato per risolvere questi problemi.

Un weak_ptr è un puntatore ad una risorsa condivisa che però non partecipa attivamente alla gestione della risorsa stessa: la risorsa viene quindi rilasciata quando si distrugge l'ultimo shared_ptr, anche se esistono dei weak_ptr che la indirizzano. Ciò significa che un weak_ptr non può accedere direttamente alla risorsa: prima di farlo, deve controllare se la risorsa è ancora disponibile. Il modo migliore per farlo è mediante l'invocazione del metodo `lock()`, che produce uno shared_ptr a partire dal weak_ptr: se la risorsa non è più disponibile, lo shared_ptr ottenuto conterrà il puntatore nullo. Ad esempio:

```
void maybe_print(std::weak_ptr<int> wp) {
  if (auto sp2 = wp.lock())
    std::cout << *sp2;
  else
    std::cout << "non più disponibile";
}

void foo() {
  std::weak_ptr<int> wp;
  {
    auto sp = std::make_shared<int>(42);
    wp = sp; // wp non incrementa il reference count della risorsa
    *sp = 55;
    maybe_print(wp); // stampa 55
  } // sp viene distrutto, insieme alla risorsa

  maybe_print(wp); // stampa "non più disponibile"
}
```