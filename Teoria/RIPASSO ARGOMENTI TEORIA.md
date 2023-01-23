    Messaoud Ramzi - Appunti Teoria Metodologie di programmazione 2021-2022
# Tipi riferimento e tipi puntatore

- un **puntatore** è un oggetto, il cui valore (un indirizzo) si può riferire ad un altro oggetto (necessariamente diverso), puo essere nullo, lavora sul oggetto puntatore
- un **riferimento** non è un oggetto vero e proprio, ma è una sorta di "alias" fornito per accedere ad un oggetto esistente, deve essere sempre inizializzato e non puo essere nullo.


Ogni volta che si effettua una operazione su un riferimento, in realtà si sta (implicitamente) operando sull'oggetto riferito.

In contrasto, nel caso dei puntatori abbiamo a che fare con due oggetti diversi: **l'oggetto puntatore e l'oggetto puntato.**
Eventuali operazioni di lettura e scrittura (comprese le operazioni relative all'aritmetica dei puntatori) applicate direttamente al puntatore accederanno e potenzialmente modificheranno l'oggetto puntatore; per lavorare sull'oggetto puntato, invece, occorrerà usare l'operatore di dereferenziazione, in una delle forme
  ``*p     // operator* prefisso``
  
  ``p->a   // operator-> infisso, equivalente a (*p).a``

# ODR: One Definition Rule
La regola della definizione unica (ODR) è una regola che stabilisce come dovrebbero essere definite e utilizzate le entità nel programma. La regola della definizione unica è stata introdotta per evitare ambiguità e incongruenze nei programmi C++.

La ODR (regola della definizione unica) stabilisce quanto segue:

 1) ogni unità di traduzione che forma **un programma può contenere non più di una definizione** di una data variabile, funzione, classe, enumerazione o template;

 2) ogni programma **deve contenere esattamente una definizione** di ogni variabile e di ogni funzione non-inline usate nel programma;

 3) ogni **funzione inline deve essere definita in ogni unità di traduzione che la utilizza**;

 4) in un programma vi possono essere più definizioni di una classe, enumerazione, funzione inline, template di classe e template di funzione (in unità di traduzione diverse, in virtù del punto 1) a condizione che:
            a) tali definizioni siano sintatticamente identiche;
            b) tali definizioni siano semanticamente identiche.    
<br>
## VIOLAZIONI ODR:

#### Esempi di violazione del punto 1:
definizione multipla di tipo in una unità di traduzione
definizione multipla di variabile in una unità di traduzione
Le funzioni possono avere lo stesso nome ma non devono essere identiche.
<br>
#### Esempi di violazione del punto 2:
- Un caso banale è quello dell'uso di una **variabile o funzione che è stata dichiarata ma non è stata mai definita nel programma** (zero definizioni): la compilazione in senso stretto andrà a buon fine, **ma il linker segnalerà l'errore al momento di generare il codice eseguibile.**
- Più interessante è il caso delle definizioni multiple (magari pure inconsistenti) effettuate in unità di traduzione diverse:
````
--- foo.hh
int foo(int a);

--- file1.cc
#include "foo.hh"
int foo(int a) { return a + 1; }

--- file2.cc
#include "foo.hh"
int foo(int a) { return a + 2; }

--- file3.cc
#include "foo.hh"
int bar(int a) { return foo(a); }
````
Il linker, al momento di collegare file3.cc con il resto del programma potrebbe invocare la funzione foo definita in file1.cc o quella definita in file2.cc, ottenendo effetti non prevedibili. Tipicamente, il linker segnalerà l'errore, ma in realtà non è tenuto a farlo.
<br>
#### Esempi di violazione del punto 3:
Il punto 3 dice che le funzioni inline devono essere definite ovunque sono usate
<br>
#### Esempi di violazione del punto 4:
Il punto più interessante della ODR è il 4; si applica a classi, enumerazioni, funzione inline, template di classe e template di funzione, ma per comprendere il problema è sufficiente considerare il caso delle definizioni di classi.

Esempio di violazione della 4a:
````
----- file1.cc
struct S { int a; int b; }; // definizione del tipo S
S s;         // definizione di variabile di tipo S
----- file2.cc
struct S { int b; int a; }; // definizione del tipo S (sintassi diversa)
extern S s;  // dichiarazione pura della s definita in file1.cc
````
Quando il compilatore lavora su file2.cc, viene ingannato dalla diversa definizione del tipo S, ma non se ne può accorgere. **Non è nemmeno detto che se ne accorga il linker** (in ogni caso, non è tenuto a farlo).
<br>
Esempio di violazione della 4b:
````
----- file1.cc
typedef T int;
struct S { T a; T b; }; // definizione del tipo S
----- file2.cc
typedef T double;
struct S { T b; T a; }; // definizione del tipo S
                        // (sintassi identica, ma semantica diversa)
````


Avendo analizzato le clausole che compongono la ODR, rimane da capire cosa va fatto all'atto pratico per garantirne la soddisfazione.

La linea guida principale, già nominata, è la DRY (don't repeat yourself), a volte detta anche "write once":

# Guardie contro l'inclusione ripetuta
Le guardie contro la definizione doppia sono generalmente costituite da una serie di direttive del preprocessore del compilatore C++, come ad esempio #ifndef, #define e #endif. Una volta che una guardia contro la definizione doppia viene definita in un file di intestazione, il compilatore la utilizzerà per verificare se il file è già stato incluso in precedenza nel file sorgente. Se il file non è stato incluso, il codice all'interno della guardia viene eseguito; se invece il file è già stato incluso, il codice all'interno della guardia viene ignorato.

esempio:
````
--- Razionale.hh
#ifndef RAZIONALE_HH_INCLUDE_GUARD
#define RAZIONALE_HH_INCLUDE_GUARD 1

class Razionale {
  // ... le cose giuste
};

#endif /* RAZIONALE_HH_INCLUDE_GUARD */
---
````


# Categorie di espressioni: lvalue e rvalue

Le espressioni del C++ possono essere classificate in
  1. lvalue  (left value)
  2. xvalue  (expiring lvalue)
  3. prvalue (primitive rvalue)

- L'unione di lvalue e xvalue forma i glvalue (generalized lvalue).
- L'unione di xvalue e prvalue forma gli rvalue (right value).
- Un lvalue è un glvalue che non sia un xvalue.
- Un prvalue è una espressione che denota un valore "primitivo", ovvero un valore costante o il risultato di una computazione.

Un xvalue è un glvalue che denota un oggetto le cui risorse possono essere riutilizzate, tipicamente perché sta terminando il suo lifetime (expiring lvalue).

````
Esempio:
Matrix foo1() {
  Matrix m;
  // ... codice
  return m; // l'espressione m è un xvalue
  //il valore ritornato dalla funzione non è m, ma una sua "copia".
}

void foo2() {
  Matrix m1;
  m1 = foo1(); // l'espressione foo1(), cioè il risultato ottenuto dalla chiamata di   //funzione, è un xvalue
}
````

La classificazione delle espressioni in lvalue, xvalue e prvalue è rilevante per capire la differenza tra riferimenti a lvalue (T&) e riferimenti a rvalue (T&&). Questi ultimi sono stati introdotti
nel C++ 2011 per risolvere problemi tecnici del linguaggio che impedivano di fornire implementazioni efficienti per alcuni costrutti.


# Le tre fasi della risoluzione dell'overloading di funzione

La risoluzione dell'overloading è un processo svolto *staticamente* dal compilatore, quando trasforma in assembler il codice contenuto nell'unità di traduzione (prodotta dal preprocessore).
Fasi: 
## 1) individuazione delle funzioni candidate;
      a) hanno lo stesso nome della funzione chiamata
      b) sono visibili nel punto della chiamata  
      
La regola ADL (Argument Dependent Lookup), detta anche Koenig's lookup,
stabilisce chenel caso di una chiamata di funzione NON qualificata, se vi sono (uno o più) argomenti "arg" aventi un tipo definito dall'utente (cioè hanno tipo struct/class/enum S, o riferimento a S o puntatore a S, possibilmente qualificati) e il tipo suddetto è definito nel namespace N
allora la ricerca delle funzioni candidate viene effettuta anche all'interno del namespace N.

## 2) selezione delle funzioni utilizzabili

Per decidere se una funzione candidata è utilizzabile, è necessario verificare che:

- il numero degli argomenti (nella chiamata di funzione) sia compatibile con il numero parametri (nella dichiarazione di funzione)

- ogni argomento (nella chiamata di funzione) abbia un tipo compatibile con il corrispondente parametro (nella dichiarazione di funzione)

## 3) scelta della migliore funzione utilizzabile (se esiste).
Sia N il numero di funzioni utilizzabili:
se N = 0, allora ho un errore di compilazione;
se N = 1, allora l'unica utilizzabile è la migliore;
se N > 1, allora occorre classificare le funzioni utilizzabili  in base alla "qualità" delle conversioni richieste; se la classificazione (spiegata sotto) determina una unica vincitrice, quella funzione è la migliore utilizzabile; altrimenti si ottiene un errore di compilazione (chiamata ambigua)

# Progettazione di un tipo concreto (sommario)

- ## **Test Driven Design** (a livello intuitivo, non in senso formale)
Mettendosi nei panni dell'utilizzatore del tipo di dato, si scrive il codice di test delle singole funzionalità; l'attività di sviluppo dell'interfaccia e dell'implementazione del tipo viene quindi "guidata" dai test (facendosi anche aiutare dalle segnalazioni di errore fornite
dal compilatore in senso lato).

L'attività di test dinamico è essenziale per verificare che la classe che si sta implementando si comporta correttamente nei casi coperti dai test stessi.

- ## **Invariante di classe**
Proprietà che deve essere soddisfatta dalla rappresentazione scelta per il tipo di dato: quando possibile, è utile codificarne il controllo in un metodo della classe (che noi abbiamo chiamato check_inv) e verificarne la validità mediante le asserzioni.

l'invariante può essere violata temporaneamente, **durante** l'esecuzione di un metodo della classe.

Occorre stare anche attenti a non fornire all'utente della classe la possibilità di "rompere" (magari inavvertitamente) la proprietà invariante. Per esempio, tramite i metodi pubblici.

- ## Precondizioni e postcondizioni

L'invariante di classe è parte delle precondizioni e postcondizioni dei metodi della classe. Queste formano il "contratto" che lo sviluppatore stabilisce con l'utilizzatore della classe.

# Programmazione per contratto

Una corretta definizione dell'interfaccia di una classe prevede la stesura di una sorta di contratto tra lo sviluppatore della classe e l'utilizzatore della classe. Per ogni funzionalità fornita, il contratto stabilisce quali sono le *precondizioni* che l'utilizzatore deve soddisfare per potere invocare la funzionalità e quali sono le *postcondizioni* che l'implementatore deve garantire in seguito all'esecuzione della funzionalità.
Tra le precondizioni e le postcondizioni sono sempre incluse le invarianti di classe sugli oggetti che sono acceduti (in lettura e/o scrittura) durante l'implementazione della funzionalità.

se l'utente non soddisfa una precondizione l'implementatore non ha alcun obbligo.

## Contratti narrow e contratti wide

Nei contratti narrow, l'implementatore si impegna a fornire la funzionalità solo quando ha senso farlo, cioè quando i valori forniti in input sono legittimi; l'onere di verificare tale legittimità è lasciato all'utilizzatore.
(per esempio, spetta all'utente controllare che un std::vector non sia vuoto prima di eliminare l'ultimo elemento usando il metodo pop_back).

Nel caso dei "contratti wide" (ampi), nei quali l'onere di verificare la legittimità delle invocazioni
ricade sull'implementatore. Scegliere un contratto wide equivale quindi a spostare alcuni elementi del contratto dal lato della precondizione al lato della postcondizione.
Non basta che i controlli vengono fatti tramite asserzioni ma i controlli devono essere parte inclusa del codice.

# I contratti per il linguaggio C++ e la libreria standard

- **comportamento specificato (specified behavior)**
- **comportamento definito dall'implementazione (implementation-defined)**
- **comportamento non specificato (unspecified behavior)**: comportamento che dipende dall'implementazione
- **comportamento non definito (undefined behavior)**: comportamento ottenuto a causa della violazione di una precondizione, in seguito alla quale l'implementazione non ha più nessuna prescrizione da seguire e quindi potrebbe comportarsi in modo totalmente arbitrario;
> esempi: indicizzazione di un array al di fuori dei limiti; tentativo di scrivere su di un oggetto definito const; overflow sui tipi interi con segno.

# GESTIONE RISORSE

Una problematica rilevante quando si sviluppa software è quella di riuscire ad ottenere una corretta gestione delle risorse.

in linea di massima, l'interazione del software con le risorse deve avvenire secondo uno schema predefinito, suddiviso in tre fasi ordinate temporalmente:

  1) acquisizione della risorsa
  2) uso della risorsa
  3) restituzione (rilascio) della risorsa

In particolare:
  * la risorsa deve essere acquisita prima di essere usata (o rilasciata);
  * al termine del suo utilizzo la risorsa deve essere rilasciata;
  * non è lecito usare una risorsa dopo averla rilasciata (se occorre, si può però ripartire dalla fase 1)

## Exception safety

Però non tutto sempre fila liscio, possono avvenire problemi fuori dal nostro controllo.
la tecnica idiomatica per segnalare situazioni di errore consiste nel lanciare eccezioni, facendo quindi in modo che il programma esca dal normale flusso di esecuzione e entri nei cosiddetti flussi di
esecuzione "eccezionali". 
Quando eseguite in modalità eccezionale, quasi tutto il codice viene ignorato. L'unico modo di rientrare nel flusso di esecuzione normale è di catturare l'eccezione (in un blocco catch) e gestirla; in assenza di un blocco catch adeguato, il programma giungerà a terminazione in modalità eccezionale.

Esistono tre diversi livelli di exception safety: base, forte e nothrow.

* ### Livello base

Una porzione di codice (una funzione o una classe) si dice exception safe a livello base se, anche nel caso in cui si verifichino delle eccezioni durante la sua esecuzione:

  1. non si hanno perdite di risorse
  2. si è neutrali rispetto alle eccezioni
  3. anche in caso di uscita in modalità eccezionale, gli oggetti sui quali si stava lavorando sono distruggibili senza causare comportamenti non definiti

Neutrali rispetto alle eccezioni:  una segnalazione di errore tramite eccezione deve essere propagata al chiamante, che così potrà prenderne atto.

* ### Livello forte (strong)
Il livello forte di exception safety si ottiene quando, oltre a tutte le garanzie fornite dal livello base, si aggiunge come ulteriore garanzia una sorta di "atomicità" delle operazioni (tutto o niente).
Intuitivamente, l'invocazione di una funzione exception safe forte, in caso di eccezione, garantisce che lo stato degli oggetti manipolati è rimasto inalterato, identico allo stato precedente la chiamata.

* ### Livello nothrow
E' il livello massimo: una funzione è nothrow se la sua esecuzione è garantita non terminare in modalità eccezionale.

Si noti che il livello nothrow, per definizione, NON è neutrale rispetto alle eccezioni.


# IDIOMA RAII-RRID: 
RAII: Resource Acquisition Is Initialization
RRID: Resource Release Is Destruction

Questo idioma è particolarmente utile per evitare problemi di memoria e di gestione delle risorse, come ad esempio i leak di memoria o i file lasciati aperti. Inoltre, rende il codice più semplice e più robusto, poiché le risorse vengono gestite in modo automatico e non è necessario preoccuparsi di liberarle manualmente.

````
class Gestore_Risorsa {
private:
  Risorsa* res_ptr;
public:
  // Costruttore: acquisisce la risorsa (RAII)
  Gestore_Risorsa() : res_ptr(acquisisci_risorsa_exc()) { }

  // Distruttore: rilascia la risorsa (RRID)
  ~Gestore_Risorsa() {
    // Nota: si assume che restituisci_risorsa si comporti correttamente
    // quando l'argumento è il puntatore nullo; se questo non è il caso,
    // è sufficiente aggiungere un test prima dell'invocazione.
    restituisci_risorsa(res_ptr);
  }

  // Disabilitazione delle copie
  Gestore_Risorsa(const Gestore_Risorsa&) = delete;
  Gestore_Risorsa& operator=(const Gestore_Risorsa&) = delete;

  // Costruzione per spostamento (C++11)
  Gestore_Risorsa(Gestore_Risorsa&& y)
    : res_ptr(y.res_ptr) {
    y.res_ptr = nullptr;
  }

  // Assegnamento per spostamento (C++11)
  Gestore_Risorsa& operator=(Gestore_Risorsa&& y) {
    restituisci_risorsa(res_ptr);
    res_ptr = y.res_ptr;
    y.res_ptr = nullptr;
    return *this;
  }

  // Accessori per l'uso (const e non-const)
  const Risorsa* get() const { return res_ptr; }
  Risorsa* get() { return res_ptr; }

  // Alternativa agli accessori: operatori di conversione implicita
  // operator Risorsa*() { return res_ptr; }
  // operator const Risorsa*() const { return res_ptr; }

}; // class Gestore_Risorsa

#endif // GUARDIA_risorsa_raii_hh
````

# SMART POINTER

L'idioma RAII-RRID si presta bene a neutralizzare la maggior parte di questi errori, rendendoli molto meno probabili; d'altra parte, scrivere una classe RAII per ogni tipo T ogni volta che si vuole
usare un T* è operazione noiosa, ripetitiva e comunque soggetta a errori.
La libreria standard viene però in aiuto, fornendo delle classi templatiche che forniscono diverse tipologie di puntatori "smart": unique_ptr, shared_ptr e weak_ptr. Le tre classi templatiche sono
definite nell'header file `<memory>`.

N.B. possono essere usati solo per l'allocazione dinamica, non per quella statica o automatica.

- **std::unique_ptr**

Uno `unique_ptr<T>` è un puntatore smart ad un oggetto di tipo T.
In particolare, unique_ptr implementa il concetto di puntatore "owning", ovvero un puntatore che si considera l'unico proprietario della risorsa. Questo puntatore non è copiabile ma solo spostabile cioè sposta il titolo di proprietario ad un altro puntatore.

````
#include <memory>

void foo() {
  std::unique_ptr<int> pi(new int(42));
  std::unique_ptr<double> pd(new double(3.1415));
  *pd *= *pd; // si dereferenzia come un puntatore
  // altri usi ...
} // qui termina il tempo di vita di pi e pd e viene rilasciata la memoria

void bar() {
  std::unique_ptr<int> pj(new int(42));
  // foo(pj);         // errore di compilazione: copia non ammessa
  foo(std::move(pj)); // ok: spostamento ammesso
  // dopo lo spostamento, pj non gestisce nessuna risorsa
}
````

- **std::shared_ptr**
Uno `shared_ptr<T>` è un puntatore smart ad un oggetto di tipo T.
Lo shared_ptr implementa il concetto di puntatore per il quale la responsabilità della corretta gestione della risorsa è "condivisa": intuitivamente, ogni volta che uno shared_ptr viene *copiato*,
l'originale e la copia condividono la responsabilità della gestione della (stessa) risorsa. A livello di implementazione, la copia causa l'incrementato di un contatore del numero di riferimenti alla risorsa (reference counter); quando uno shared_ptr viene distrutto, decrementa il reference counter associato alla risorsa e, se si accorge di essere rimasto l'unico shared_ptr ad avervi ancora accesso, ne effettua
il rilascio (informalmente, si dice che "l'ultimo chiude la porta").

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

- std::weak_ptr
Un `weak_ptr` è un puntatore ad una risorsa condivisa che però non partecipa attivamente alla gestione della risorsa stessa: la risorsa viene quindi rilasciata quando si distrugge l'ultimo shared_ptr, anche se esistono dei weak_ptr che la indirizzano. Ciò significa che un weak_ptr non può accedere direttamente alla risorsa: prima di farlo, deve controllare se la risorsa è ancora disponibile.

## I template di funzione std::make_shared e std::make_unique

````
void bar() {
  auto pi = std::make_shared<int>(42);
  auto pj = std::make_shared<double>(3.1415);
}
````

Oltre all'efficienza, l'uso di std::make_shared consente di evitare alcuni errori subdoli che potrebbero compromettere la corretta gestione delle risorse in presenza di comportamenti eccezionali.

Esempio:
````
void bar(std::shared_ptr<int> pi,
         std::shared_ptr<int> pj);

void foo() {
  // codice NON exception safe
  bar(std::shared_ptr<int>(new int(42)),
      std::shared_ptr<int>(new int(42)));

  // codice exception safe
  bar(std::make_shared<int>(42),
      std::make_shared<int>(42));
}
````

# TEMPLATE

*Template di funzione*

Un template di funzione è un costrutto del linguggio C++ che consente di scrivere un *modello* (schema) parametrico per una funzione.

````
// dichiarazione pura di un template di funzione
template <typename T>
T max(T a, T b);

// definizione di un template di funzione
template <typename T>
T max(T a, T b) {
  return (a > b) ? a : b;
}
````

*Istanziazione di un template di funzione:*

Dato un template di funzione, è possibile "generare" da esso una o più funzioni mediante il meccanismo di istanziazione (del template):
l'istanziazione fornisce un *argomento* (della tipologia corretta) ad ognuno dei parametri del template.

*N.B.* un template di funzione NON è una funzione (è un "generatore" di funzioni); una istanza di un template di funzione è una funzione.


*Specializzazione esplicita di un template di funzione*

una specializzazione di un template è una versione specifica di un template che viene utilizzata in determinate condizioni. Ad esempio, si può specializzare un template per un tipo specifico, come ad esempio un template per la classe "MyClass" che ha una specializzazione per il tipo "int". In questo caso, quando si crea un'istanza di `MyClass<int>`, il compilatore utilizzerà la specializzazione per "int" invece della versione generale del template.

Esempio:
````
template<typename T>
class MyClass {
 public:
  T value;
  MyClass(T v) : value(v) {}
  void printValue() {
    std::cout << value << std::endl;
  }
};

// Specializzazione del template per il tipo int
template<>
class MyClass<int> {
 public:
  int value;
  MyClass(int v) : value(v) {}
  void printValue() {
    std::cout << "Valore intero: " << value << std::endl;
  }
};

int main() {
  // Utilizzo dell'istanza del template per creare un oggetto di tipo MyClass<int>
  MyClass<int> myInt(5);
  myInt.printValue(); // Stampa "Valore intero: 5"
  
  // Utilizzo dell'istanza del template per creare un oggetto di tipo MyClass<string>
  MyClass<string> myString("hello");
  myString.printValue(); // Stampa "hello"
  return 0;
}
````

*Istanziazioni e specializzazioni di template di classe*
Come nel caso dei template di funzione, anche i template di classe possono essere istanziati (implicitamente o esplicitamente) e specializzati.

Le istanziazioni di template di classe vengono create specificando i parametri di tipo per il template, ad esempio:
````
MyClass<int> myInt;
MyClass<string> myString;
MyClass<T> myObject;
````

Le specializzazioni di template di classe, d'altra parte, forniscono una versione specifica del codice per un tipo specifico. Si può fare una specializzazione per un tipo specifico, un gruppo di tipi o per tutti i tipi.

````
template<>
class MyClass<int> { /*...*/ };

````
In questo modo, quando si crea un'istanza di `MyClass<int>`, il compilatore utilizzerà la specializzazione per "int" invece della versione generale del template.


### COMPILAZIONE DEI TEMPLATE

Il processo di compilazione dei template richiede lo stesso codice sia analizzato dal compilatore in (almeno) due contesti distinti:
  1) al momento della definizione del template
  2) al momento della instanziazione del template

Una prima conseguenza, di cui tenere conto quando si scrivono i programmi, è che la definizione di un template deve essere disponibile i tutti i punti del programma nei quali se ne richiede l'istanziazione. Uno dei modi è:

includere le definizioni dei template (comprese la definizioni di eventuali funzioni membro dei template di classe) prima di ogni loro uso nella unità di traduzione;

# Programmazione generica in C++

I template vengono usati in C++ per realizzare il "polimorfismo statico":

 * si parla di "polimorfismo" in quanto si scrive una sola versione del codice (template) che però viene utilizzata per generare tante varianti (istanze) e quindi può assumere tante forme concrete;

 * il polimorfismo è "statico" in quanto la scelta delle istanze da generare avviene staticamente, a tempo di compilazione; cioè non avviene a run-time, come nel caso del polimorfismo "dinamico".


- #### I CONTENITORI
Un contenitore è una classe che ha lo scopo di contenere una collezione di oggetti (spesso chiamati elementi del contenitore);

- ##### I contenitori sequenziali
I contenitori sequenziali forniscono accesso ad una sequenza di elementi, organizzati in base alla loro posizione (il primo elemento, il secondo, il terzo, ecc.).  L'ordinamento degli oggetti nella sequenza non è stabilito in base ad un criterio di ordinamento stabilito a priori, ma viene dato dalle specifiche operazioni di inserimento e rimozione degli elementi.

I contenitori sequenziali standard sono:
- `std::vector<T>`
    sequenza di T di dimensione variabile (a tempo di esecuzione), memorizzati in modo contiguo;
- `std::deque<T>`
una "double-ended queue" è una coda a doppia entrata, nella quale inserimenti e rimozioni efficienti possono essere effettuati sia in fondo alla sequenza (come nel caso dei vector) che all'inizio della sequenza.
- `std::list<T>`
sequenza di T di dimensione variabile (a tempo di esecuzione), memorizzati (in modo non contiguo) in una struttura a lista doppiamente concatenata; la doppia concatenazione consente lo scorrimento della lista sia in avanti che all'indietro (bidirezionale); per accedere ad un elemento occorre "raggiungerlo" seguendo i link della lista.
- `std::forward_list<T>`
come una list, ma la concatenazione tra nodi è singola e quindi è consentito lo scorrimento solo in avanti (forward).

- pseudo-contenitori
- `std::array<T, N>` sequenza di T di dimensione N
- `std::string` può essere visto come una sequenza di caratteri (char)

##  Algoritmi generici
un algoritmo generico è un algoritmo che può essere utilizzato per risolvere una categoria di problemi piuttosto che un problema specifico. In altre parole, un algoritmo generico è un algoritmo che può essere adattato a lavorare con tipi di dati diversi senza modificare la logica dell'algoritmo stesso.

In C++, gli algoritmi generici sono spesso implementati utilizzando i template per permettere di utilizzare la stessa logica dell'algoritmo con tipi di dati diversi. Ad esempio, l'algoritmo di ordinamento "sort" è un algoritmo generico che può essere utilizzato per ordinare array di qualsiasi tipo di dato che supporta l'operatore di confronto "<".

Gli algoritmi generici possono essere utilizzati per ridurre la quantità di codice da scrivere, poiché un singolo algoritmo può essere utilizzato per risolvere problemi simili con tipi di dati diversi.

Gli elementi dei algoritmi generici possono essere rappresentati come una sequenza compresa tra un intervallo `[first, last)` in qui ci si muove con un iteratore.

## ITERATORI
Per poter istanziare il concetto di iteratore Iter deve:

1) Iter deve supportare la copia (passato e restituito per valore)
2) Iter deve supportare il confronto binario (first != last), per capire se la sequenza è terminata o meno
3) Iter deve supportare il preincremento (++first), per avanzare di una posizione nella sequenza
4) Iter deve consentire la dereferenziazione (*first), per poter leggere il valore "puntato"
5) il tipo dei valori puntati da Iter deve essere confrontabile con il tipo T (usando l'operatore == )

Molti algoritmi generici della libreria standard lavorano sul concetto di sequenza. Il concetto di iteratore, che prende spunto dal puntatore,fornisce un modo efficace per rappresentare varie tipologie di sequenze, indipendentemente dal tipo concreto usato per la loro implementazione.

Gli iteratori si possono classificare in 5 categorie distinte (che corrispondono, tecnicamente, a 5 concetti correlati ma distinti), che si differenziano per le operazioni supportate e per le corrispondenti garanzie fornite all'utente. Le categorie sono:

-   *Iteratori di input*: consentono di leggere gli elementi di un contenitore, ma non permettono di modificarli. Gli operatori che possono essere utilizzati con gli iteratori di input sono: `*`, `->`, `++`, `--`, `==`, `!=`

-  *Iteratori forward* : Consentono di effettuare tutte le operazioni supportate dagli iteratori
di input. Inoltre, l'operazione di avanzamento effettuata su un iteratore forward NON invalida eventuali altri iteratori che puntano ad elementi precedenti nella sequenza (cioè, gli iteratori forward sono riavvolgibili e consentono di scorrere più volte la stessa sequenza).

-   *Iteratori di output*: consentono di scrivere gli elementi di un contenitore, ma non permettono di leggere da essi. Gli operatori che possono essere utilizzati con gli iteratori di output sono: `*`, `=`, `++`, `--`
    
-   *Iteratori bidirezionali*: consentono sia di leggere che di scrivere gli elementi di un contenitore, e permettono anche di iterare in entrambe le direzioni. Gli operatori che possono essere utilizzati con gli iteratori bidirezionali sono: `*`, `->`, `++`, `--`, `==`, `!=`
    
-   *Iteratori random access*: consentono sia di leggere che di scrivere gli elementi di un contenitore, e permettono anche di accedere agli elementi in modo casuale attraverso l'operatore di indice `[]` e gli operatori `+=`, `-=`, `+`, `-` e `<`, `>`, `<=`, `>=`.

esempio:
````
std::forward_list<int> myList;
std::forward_list<int>::iterator it = myList.begin();
*it = 5;
++it;
````

## INSERITORI

3 tipi:

- `std::back_insert_iterator` inserisce sempre infondo

- `std::insert_front_iterator` inserisce sempre davanti (non funziona con vector)

- `std::Insert_iterator` inserisce nella posizione che li forniamo.

devo includere 
````
#include <iterator> 
#include <iostream>
````

possiamo usare la deduzione automatica con 

`std::back_inserter`
`std::front_inserter`
`std::inserter`

per risparmiare di scrivere tutto il tipo.  
<br>
Gli inseritori sono oggetti funzionali definiti nella libreria standard di C++ che consentono di inserire elementi in un container. Gli inseritori sono implementati come iteratori di output e sono basati sul modello di iteratore inseritore di output (std::insert_iterator). Gli inseritori vengono utilizzati per inserire elementi in un container in modo semplice e rapido, senza dover scrivere codice specifico per ogni tipo di container.  



La libreria standard fornisce tre *specializzazioni degli inseritori*: std::back_inserter, std::front_inserter e std::inserter.

-   std::back_inserter è un iteratore di output che consente di inserire elementi in un container di tipo sequenza alla fine della sequenza.
-   std::front_inserter è un iteratore di output che consente di inserire elementi in un container di tipo sequenza all'inizio della sequenza.
-   std::inserter è un iteratore di output che consente di inserire elementi in un container di tipo associativo (ad esempio, un std::map) in base all'ordine di chiave.
<br>

**back_inserter**: crea un oggetto inseritore che utilizza il metodo push_back() per inserire elementi in un contenitore. Ad esempio:
````
#include <algorithm>
#include <vector>

std::vector<int> v {1,2,3};
std::copy(v.begin(), v.end(), std::back_inserter(v));
````
Questo esempio utilizza la funzione standard copy per copiare gli elementi di un vettore in un altro vettore utilizzando il back_inserter.
<br>
**front_inserter**: crea un oggetto inseritore che utilizza il metodo push_front() per inserire elementi in un contenitore. Ad esempio:
````
#include <algorithm>
#include <deque>

std::deque<int> d {1,2,3};
std::copy(d.begin(), d.end(), std::front_inserter(d));
````
Questo esempio utilizza la funzione standard copy per copiare gli elementi di un deque in un altro deque utilizzando il front_inserter.
<br>
**inserter**: crea un oggetto inseritore che utilizza l'operatore di inserimento () per inserire elementi in un contenitore. Ad esempio:

````
#include <algorithm>
#include <list>

std::list<int> l {1,2,3};
std::copy(l.begin(), l.end(), std::inserter(l, l.begin()));
````
Questo esempio utilizza la funzione standard `copy` per copiare gli elementi di una list in un'altra list utilizzando l'inseritore. In questo caso si specifica un iteratore di inserimento per inserire gli elementi all'inizio della list.

In generale, gli inseritori vengono utilizzati per inserire elementi in un contenitore senza dover utilizzare il costruttore di copia o il costruttore di assegnazione, e quindi migliorando le prestazioni del codice.

# IL CONCETTO CALLABLE
Il concetto di "callable" in C++ indica qualsiasi oggetto che può essere chiamato come una funzione. Ci sono tre tipi di oggetti callable in C++: le funzioni, i membri funzione e gli oggetti funzione.

-   Le funzioni sono oggetti callable per definizione, essi possono essere chiamati utilizzando il loro nome e i parametri appropriati.
    
-   I membri funzione sono funzioni membro di una classe, essi possono essere chiamati utilizzando il nome della funzione membro e un puntatore o un riferimento all'oggetto della classe.
    
-   Gli oggetti funzione sono oggetti che possono essere chiamati come funzioni, essi includono funzioni anonime, funzioni lambda e funzioni di membro statico.
    

Il concetto di callable è molto utile quando si vuole passare una funzione o un oggetto funzione come parametro a un'altra funzione o utilizzarlo come una funzione di callback. Inoltre, gli oggetti callable possono essere utilizzati in combinazione con funzioni standard come `std::for_each`, `std::sort` e `std::bind` per personalizzare il comportamento delle funzioni standard.

#### LE FUNZIONI LAMBDA
Le funzioni lambda in C++ sono una forma di funzione anonima, che significa che non hanno un nome. Esse vengono utilizzate per creare piccole funzioni ad-hoc, spesso utilizzate come parametri per altre funzioni. Le funzioni lambda vengono create utilizzando la sintassi:

`[capture list](parameter list) -> return type { function body }`

-   La lista di cattura: è un elenco di variabili che verranno catturate dalla funzione lambda. Queste variabili possono essere utilizzate all'interno del corpo della funzione lambda.
-   La lista di parametri: è un elenco di parametri che verranno passati alla funzione lambda quando verrà chiamata.
-   Il tipo di ritorno: è il tipo di valore restituito dalla funzione lambda. Se non viene specificato, il compilatore dedurrà il tipo di ritorno in base al corpo della funzione.
-   Il corpo della funzione: è la logica della funzione lambda.

Ad esempio, una funzione lambda che accetta due interi e restituisce la loro somma sarebbe scritta come:

`[](int a, int b) -> int { return a + b; }`

Inoltre, le funzioni lambda possono essere utilizzate come parametri per funzioni come `std::for_each`, `std::sort` e `std::bind`.

`std::vector<int> v{1,2,3,4,5}; std::for_each(v.begin(), v.end(), [](int& x){x*=2;});`

In questo esempio, la funzione lambda moltiplica ogni elemento del vettore per 2.

# Overloading template di funzione
I template di funzione dotati dello stesso nome e visibili nello stesso scope (i.e., possono andare in overloading) sono ordinati parzialmente rispetto ad una relazione di "specificità".

Intuitivamente, denotiamo con  istanze(X) l'insieme di tutte le possibili istanze del template X, allora si dice che il template di funzione X è più specifico del template di funzione Y se istanze(X) è un sottoinsieme proprio di istanze(Y).

*Regola speciale per i template di funzione:*
nella risoluzione dell'overloading, le istanze dei template più specifici sono preferite a quelle dei template meno specifici.

Per decidere se (una istanza di) un template di funzione entra nell'insieme delle candidate occorre verificare se è possibile effettuare la deduzione dei parametri del template. Durante il
processo di deduzione NON si possono applicare promozioni, conversioni standard e conversioni definite dall'utente (in pratica, si possono applicare solo le corrispondenze esatte).

Nota: se il processo di deduzione ha successo, allora l'istanza del template oltre ad essere candidata è anche utilizzabile.

Quando si cerca la migliore funzione utilizzabile, per quanto detto sopra, si devono eliminare le istanze ottenute da template di funzione meno specifici (che altrimenti causerebbero ambiguità).
Se anche dopo avere fatto questa rimozione continuiamo ad avere ambiguità, si eliminano dalle utilizzabili tutte le istanze di template. In questo modo, viene data una preferenza alle funzioni non templatiche.

Candidate: solo match esatti 
- se la mia chiamata ha due argomenti di tipi uguali, sono candidate solo i template di funzioni con due argomenti uguali
- Se la mia chiamata prende due argomenti diversi, sono candidate solo i template di funzione con due argomenti diversi (non sono candidate quelle con due uguali).

Utilizzabili: 
- tutte le Candidate sono automaticamente utilizzabili

Migliore:
- vengono rimosse tutte quelle meno specifiche, cercando di lasciare la più specifica
- se ce ambiguità, nessuna è migliore e si sceglie una funzione non templetatica (se esiste)


# **POLIMORFISMO DINAMICO**

## CLASSI DINAMICHE

#### Classi derivate e relazione "IS-A

Si consideri una classe Base e una classe Derived derivata pubblicamente dalla classe Base:

````
class Base {
  /* ... omissis ... */
};

class Derived : public Base {
  /* ... omissis ... */
};
````

Si dice che la classe Derived è in relazione "IS-A" con la classe Base, cioè è una particolare concretizzazione della classe Base, e quindi deve potere essere utilizzato, dall'utente, come se fosse un oggetto di tipo Base. In altre parole, in questo contesto l'utente vuole lavorare
con oggetti di tipo Base, ignorando eventuali differenze tra le varie concretizzazioni possibili.

la derivazione pubblica consente di effettuare, in maniera implicita, le conversioni di tipo dette *up-cast*, ovvero la conversione da un puntatore o riferimento per un oggetto Derived
verso un puntatore o riferimento per un oggetto Base.

  `Base* base_ptr = new Derived;`

#### Metodi virtuali e classi dinamiche

Una classe che contenga almeno un metodo virtuale viene detta "classe dinamica", in quanto per gli oggetti di questa classe vengono messe a disposizione le funzionalità che consentono di implementare la risoluzione dell'overriding e, più in generale, la RTTI (Run-Time Type Identification).

una classe derivata da Base che lo ridefinisca (usando lo stesso nome e lo stesso numero e tipo degli argomenti) ne fa l'overriding; si noti che, se il metodo non fosse stato dichiarato virtual nella classe base, NON si avrebbe overriding, ma si avrebbe invece hiding.
Nella classe derivata non è necessario (ma è consentito) ripetere la parola chiave virtual. Se si uso lo standard C++11 o superiore è anche consigliato usare la parola chiave "override", da usarsi alla fine della dichiarazione.

L'uso di "override" è utile perché causa un errore nel caso in cui nella classe base Printer non esista un metodo virtuale corrispondente.
L'errore è meno frequente di quanto si possa immaginare, perché:
  - potremmo esserci dimenticati di usare la parola chiave virtual
    nella classe base;
  - potremmo avere modificato leggermente il tipo del metodo, cambiando
    il numero o il tipo dei parametri.

#### Metodi virtuali puri e classi astratte

Invece di fornire una implementazione fittizia del metodo virtual in Base è preferibile indicare che il metodo virtuale è "puro", usando la sintassi "= 0" al termine della sua dichiarazione.

Una classe che contenga metodo virtuali puri è detta "**classe astratta**"

Il fatto che un metodo virtuale sia puro significa che ogni classe concreta che eredita dalla classe astratta Base è tenuta a fare l'overriding del metodo; se NON fa l'overriding, il metodo rimane puro e quindi la classe derivata è anche essa una classe astratta.

#### I distruttori delle classi astratte
I distruttori delle classi astratte dovrebbero essere sempre dichiarati virtual e non dovrebbero mai essere metodi puri (ovvero, occorre fornirne l'implementazione).

````
// distruttore virtuale NON puro (definito, non fa nulla)
  virtual ~Astratta() {}

````

La ragione per questo modo di definire il distruttore è legata alla necessità di consentire una corretta distruzione degli oggetti delle classi concrete derivate dalla classe astratta. Infatti:
 1) il distruttore della classe concreta invoca (implicitamente) il distruttore delle sue classi base, che quindi deve essere definito (cioè non può essere un metodo puro);
 2) se il distruttore della classe astratta NON fosse virtuale, si avrebbero dei memory leaks.

#### Risoluzione overriding

Viene effettuata a tempo di esecuzione dal RTS (supporto a tempo di esecuzione). Si noti che, in ogni caso, a tempo di compilazione viene fatta la risoluzione dell'overloading nel solito modo.
Affinché si attivi l'overriding occorre che:

1) il metodo invocato sia un metodo virtuale (esplicitamente o implicitamente, se ereditato da una classe base);

2) il metodo viene invocato tramite puntatore o riferimento (altrimenti non vi può essere distinzione tra il tipo statico e il tipo dinamico dell'oggetto e quindi si invoca il metodo
   della classe base);

3) almeno una delle classi lungo la catena di derivazione che porta dal tipo statico al tipo dinamico ha effettuato l'overriding (in assenza di overriding, si invoca il metodo della classe base);

4) il metodo NON deve essere invocato mediante qualificazione esplicita (la qualificazione esplicita causa l'invocazione del metodo come definito nella classe usata per la qualificazione). `Base.foo(int)`


# Principi progettazione object oriented

Con l'acrostico SOLID si identificano i cosiddetti "primi 5 principi" della progettazione object oriented. Lo scopo dei principi è di fornire una guida verso lo sviluppo di progetti che siano più "flessibili", cioè progetti per i quali sia relativamente semplificato effettuare
modifiche in termini di:
  - manutenzione delle funzionalità esistenti;
  - estensione delle funzionalità supportate.

L'acrostico **SOLID**

 S ==> SRP (Single Responsibility Principle)
 O ==> OCP (Open-Closed Principle)
 L ==> LSP (Liskov Substitution Principle)
 I ==> ISP (Interface Segregation Principle)
 D ==> DIP (Dependency Inversion Principle)

il progettista dovrebbe individuare quelle parti del software che:
  - sono in contrasto con alcuni di questi principi
  - in futuro potrebbero beneficiare dall'applicazione di una ristrutturazione del codice in linea con i principi e quindi applicare le azioni correttive del caso.


- #### [[SRP]] (Principio della Responsabilità Singola): ogni classe dovrebbe avere una sola responsabilità e quindi un solo motivo per cambiare.
    - Il rispetto del principio porta a codice più manutenibile e, in linea di massima, più facile da riutilizzare.
    - Esempio:
        una classe che deve manipolare una pluralità di risorse (in maniera
        exception safe) non dovrebbe prendersi carico direttamente della
        corretta gestione dell'acquisizione e rilascio delle singole risorse.
        Piuttosto, dovrebbe *delegare* questo compito ad opportune classi gestore
        (intuitivamente, una classe gestore per ogni tipologia distinta di risorsa)
        e focalizzarsi sull'uso appropriato delle risorse.


- ####  [[OCP]] (Principio di Apertura/Chiusura): una classe dovrebbe essere aperta per l'estensione ma chiusa per la modifica.
    - SHOULD BE OPEN FOR EXTENSION, BUT CLOSED FOR MODIFICATION.

Il principio dice che:
  1) il software dovrebbe essere "aperto alle estensioni"
  2) il software dovrebbe essere "chiuso alle modifiche"

In altre parole, un software progettato bene dovrebbe rendere semplice
l'aggiunta di nuove funzionalità (scrivendo nuovo codice), senza che
per fare ciò sia necessario modificare il codice esistente (chiusura
alle modifiche).


- ####  [[LSP]] (Principio di Sostituzione di Liskov): le classi derivate dovrebbero essere sostituibili con le loro classi base.
   - Ogni funzione che usa puntatori o riferimenti a classi base deve essere in grado di usare oggetti delle classi derivate senza saperlo.
    - sono le più pericolese e le più difficili da rilevare.

- #### [[ISP]] (Principio di Segregazione delle Interfacce): le interfacce dovrebbero essere piccole e specifiche per i loro clienti.
    - Aderendo a questo principio, si ottengono i seguenti benefici:

1) l'implementatore può implementare separatamente le interfacce piccole,
   evitando che un errore su una di queste si propaghi sulle altre;

2) l'implementatore può decidere di implementare solo alcune delle
   interfacce piccole ottenute dalla separazione dell'interfaccia grande;

3) se una delle interfacce piccole dovesse cambiare, l'utente che non la
   usa (perché usa le altre) non ne è minimamente influenzato; al contrario,
   adottando una sola interfaccia grande, una modifica su una sua parte
   influenza anche gli utenti che NON fanno alcun uso di quella parte
     


- ####   [[DIP]] (Principio di Inversione delle Dipendenze): le dipendenze tra le classi dovrebbero essere basate sull'interfaccia, non sull'implementazione.
    - I moduli di alto livello non devono dipendere da quelli di basso livello:
  entrambi devono dipendere da astrazioni. Le astrazioni non devono dipendere dai dettagli; sono i dettagli che dipendono dalle astrazioni.




# Note su eredetarietà multipla

1) ci sono metodi che NON possono essere dichiarati virtuali?

In particolare, cosa si può dire sulla possibilità o meno di rendere
virtuali le seguenti categorie di metodi:
  - costruttori :(no perche non ha senso)
  - distruttori :(si per evitare memory leak)
  - funzioni membro (di istanza, cioè non statiche): (si, in teoria virtuali pure)
  - funzioni membro statiche :(no perche non puo chiamare il puntatore this->qulacosa())
  - template di funzioni membro (non statiche) ( in c++ non si puo fare, perche il linguaggio non me lo consente)
  - funzioni membro (non statiche) di classi templatiche

-----------------------------------------------------------------------

2) Come faccio a costruire una copia di un oggetto concreto quando
   questo mi viene fornito come puntatore/riferimento alla classe base?

Per costruire una copia di un oggetto concreto quando questo ti viene fornito come puntatore o riferimento alla classe base, puoi utilizzare il metodo di copia della classe base. Tuttavia, questo metodo di copia potrebbe non essere in grado di copiare correttamente tutti i dati della classe derivata.

Una soluzione per questo problema è utilizzare il polimorfismo e la sovrascrittura del metodo di copia nella classe derivata. Puoi definire un metodo di copia virtuale nella classe base che crea una copia superficiale dell'oggetto e sovrascriverlo nella classe derivata per creare una copia profonda dell'oggetto.

Esempio:

````
class Base {
public:
    virtual Base* clone() const { return new Base(*this); }
    // ... altro codice ...
};

class Derived : public Base {
public:
    virtual Derived* clone() const { return new Derived(*this); }
    // ... altro codice ...
};

int main() {
    Base* b = new Derived;
    Base* b_copy = b->clone();
}
````

-----------------------------------------------------------------------

3) Cosa succede se si invoca un metodo virtuale durante la fase di
   costruzione o di distruzione di un oggetto?
````
Base   Virtual foo()

Derived : public Base  override foo()

Derived d; //derived non viene ancora costruito (undefind behavior)
````

NON SI DEVE MAI CHIAMARE UNA FUNZIONE ALL'INTERNO DEL COSTRUTTORE O DISTRUTTORE

