

##  Spiegare brevemente il significato e la differenza tra invariante di classe, precondizioni e postcondizioni per un metodo

**Invariante di classe**: un'invariante di classe è una proprietà che deve essere sempre vera per una classe durante la sua esecuzione, a prescindere dai metodi che la modificano.


**Precondizioni**: le precondizioni sono le condizioni che devono essere soddisfatte dall'utilizzatore prima che un metodo venga eseguito.


**Postcondizioni**: le postcondizioni sono le condizioni che devono essere soddisfatte  dall'implementatore dopo che un metodo è stato eseguito.


La **differenza principale** tra invariante di classe, precondizioni e postcondizioni è il momento in cui vengono verificate e il loro scopo.


L'invariante di classe viene verificata durante tutto il ciclo di vita di un oggetto e ha lo scopo di definire il comportamento di una classe e garantire che gli oggetti di questa classe soddisfino determinate proprietà.


Le precondizioni vengono verificate prima che un metodo possa essere chiamato e hanno lo scopo di garantire che un metodo venga chiamato in modo appropriato e che i suoi input siano validi.


Le postcondizioni vengono verificate al termine dell'esecuzione di un metodo e hanno lo scopo di garantire che un metodo produca output validi e che le sue modifiche agli oggetti siano coerenti con le sue responsabilità.

-----------------------------------------------------------------

## Spiegare brevemente (aiutandosi per mezzo di semplici esempi) la differenza tra i concetti di hiding, overloading e overriding per le funzioni membro.

Il concetto di **hiding (nascondimento)** si verifica quando una funzione membro di una classe viene dichiarata con lo stesso nome e firma di una funzione membro ereditata dalla superclasse. In questo caso, la funzione membro della superclasse viene "nascosta" dalla funzione membro della classe figlia e non è più visibile o utilizzabile tramite gli oggetti della classe figlia. Tuttavia, la funzione membro della superclasse rimane disponibile per gli oggetti della superclasse e può essere utilizzata tramite il meccanismo di riferimento a superclasse. Ecco un esempio di hiding:
````
class Base { 
public: 
void foo() { /* ... */ } };

class Derived : public Base { 
public: 
void foo() { /* ... */ } // nasconde la funzione foo() della classe Base 
};

int main() { 
Derived d; 
Base& b = d; 
b->foo(); // chiama la funzione foo() della classe derived (hiding) 
}
````



Il concetto di **overloading (sovraccarico)** si verifica quando due o più funzioni membro di una classe hanno lo stesso nome, ma hanno firme diverse (ovvero, hanno un numero o un tipo di parametri differente). In questo caso, le funzioni vengono considerate come overload (sovraccarichi) della stessa funzione e possono essere distinte in base alla loro firma. L'overloading permette di utilizzare lo stesso nome per funzioni che svolgono compiti simili, ma con input leggermente differenti. Ecco un esempio di overloading:
````
class MyClass { 
public: 
void foo(int x) { /* ... _/ } 
void foo(double x) { /_ ... _/ } 
void foo(int x, double y) { /_ ... */ } };

int main() { 
MyClass obj; 
obj.foo(1); // chiama la funzione foo(int) 
obj.foo(1.0); // chiama la funzione foo(double) 
obj.foo(1, 2.0); // chiama la funzione foo(int, double) }
````



Il concetto di **overriding (riscrittura)** si verifica quando una funzione membro di una classe figlia riscrive (ovvero, ridefinisce) una funzione membro ereditata dalla superclasse.
In questo caso, la funzione membro della superclasse viene "sovrascritta" dalla funzione membro della classe figlia e viene utilizzata invece di quella della superclasse quando viene chiamata tramite un riferimento o un puntatore alla classe figlia. Tuttavia, la funzione membro della superclasse rimane disponibile per gli oggetti della superclasse e può essere utilizzata tramite il meccanismo di riferimento a superclasse. Ecco un esempio di overriding:
````
class Base { 
public: 
virtual void foo() { /* ... */ } };

class Derived : public Base { 
public: 
void foo() override { /* ... */ } // riscrive la funzione foo() della classe Base };

int main() { 
Derived d; 
d.foo(); // chiama la funzione foo() della classe Derived 
Base& b = d; 
b.foo(); // chiama la funzione foo() della classe Derived 
}
````
----------------------------------------------------------------------------
## Spiegare cosa si intende quando si dice che una porzione di codice è neutrale rispetto alle eccezioni.

Quando una porzione di codice è neutrale rispetto alle eccezioni, significa che non gestisce le eccezioni in modo specifico e non interviene direttamente per gestirle. Ciò significa che non viene eseguito alcun codice specifico per gestire le eccezioni, ma invece vengono lasciate gestire ai livelli superiori o a una funzione chiamante.

-------------------------------------------------------------------
## Spiegare brevemente cosa sono e a cosa servono gli inseritori della libreria standard. Scrivere un semplice esempio di codice in c++ che usa un inseritore.

Gli inseritori sono oggetti funzionali definiti nella libreria standard di C++ che consentono di inserire elementi in un container. Gli inseritori sono implementati come iteratori di output e sono basati sul modello di iteratore inseritore di output (std::insert_iterator). Gli inseritori vengono utilizzati per inserire elementi in un container in modo semplice e rapido, senza dover scrivere codice specifico per ogni tipo di container.
<br>
La libreria standard fornisce tre specializzazioni degli inseritori: std::back_inserter, std::front_inserter e std::inserter.

-   std::back_inserter è un iteratore di output che consente di inserire elementi in un container di tipo sequenza alla fine della sequenza.
-   std::front_inserter è un iteratore di output che consente di inserire elementi in un container di tipo sequenza all'inizio della sequenza.
-   std::inserter è un iteratore di output che consente di inserire elementi in un container di tipo associativo (ad esempio, un std::map) in base all'ordine di chiave.



Un esempio di codice in C++ che usa un inseritore può essere:
```
#include<vector>
#include<algorithm>

int main()
{
    std::vector<int> v = {10, 20, 30, 40};
 
    // Inserisci l'elemento 50 all'inizio del vettore:
    std::vector<int>::iterator it = std::inserter(v, v.begin());
    *it = 50;
 
    // Stampa tutti gli elementi del vettore:
    std::cout << "Elementi del vettore: ";
    for (int i : v)
        std::cout << i << " ";
    std::cout << std::endl;
 
    return 0;
}

Output: Elementi del vettore: 50 10 20 30 40
```
-------------------------------------------------------------------------
## Spiegare brevemente i benefici che si ottengono applicando il principio ISP (Interface Segregation Principle).

Il principio ISP (Interface Segregation Principle) è un principio di progettazione orientato agli oggetti che afferma che le interfacce utente devono essere piccole e specializzate, invece di grandi e multipurpose. Il principio ISP fornisce diversi benefici, tra cui:

1. Migliore comprensibilità: Poiché le interfacce fornite sono più piccole e specializzate, sono più facili da capire.

2. Migliore leggibilità: Le interfacce più piccole rendono più facile per gli sviluppatori leggere e comprendere il codice.

3. Meno dipendenza: Le interfacce più piccole possono ridurre le dipendenze tra le classi e le dipendenze tra i pacchetti.

4. Migliori prestazioni: Le interfacce più piccole possono ridurre le dimensioni del codice e quindi migliorare le prestazioni.

5. Maggiore scalabilità: Le interfacce più piccole e specializzate possono essere facilmente aggiornate o estese in futuro.


## Motivare brevemente l’introduzione delle guardie contro l’inclusione ripetuta dei file di intestazione, enunciando chiaramente la regola del linguaggio che verrebbe violata nel caso di un loro mancato (o scorretto) utilizzo. Scrivere un esempio, minimale ma completo, di codice che non si comporta correttamente a causa dell’assenza di tali guardie.

Le guardie contro l'inclusione ripetuta servono a evitare di includere più volte li stessi file che violerebbe le **regole ODR**, cioè che ogni unità di traduzione che forma un programma può contenere non più di una definizione di una data variabile, funzione, classe, enumerazione o template.

------------------------------------------------------

## Fornire un esempio di violazione della ODR (One Definition Rule) che genera un errore diagnosticabile in fase di compilazine in senso stretto (cioè prima della fase di collegamento).

La violazione del punto 2 delle One Definition Rule, "ogni variabile o funzione dichiarata deve essere definita nel programma" il linker nella fase di collegamento genererà errore.

------------------------------------------------------
## Fornire un esempio di chiamata di funzione nella quale si applica una conversione implicita definita dall'utente (senza usare i tipi definti nella libreria std)

````
struct Razionale {
int num;
int den;
}
/.../

void foo(int x) {...}
int main()
{
Razionale r(1,2);
foo (r) //conversione implicita da razionale a int definita dall'utente
}
````
oppure semplicemente:


````

struct Razionale {
        Razionale(int num, int den = 1); // conv. da int a Razionale
        // ...
      };
````

------------------------------------------------------
## I template di classe std::vector e std::list hanno interfacce simili, ma non identiche. Indicare almeno un metodo di std::vector che non è supportato da std::list e, viceversa, un metodo di std::list non supportato da std::vector.

------------------------------------------------------
## Dati i tipi di dato **K** e **M**, come è definito il tipo di dato `std::map<K,M>::value_type`. cioè il tipo di dato degli elementi di una mappa della libreria standard?

------------------------------------------------------
## Elencare gli operatori che si possono applicare agli iteratori random access, ma non si possono applicare agli iteratori bidirezionali.
spostarmi di un numero random (n è un valore intero)
first=+n
first=-n
iter1-iter2 (distanza tra i due iteratori)
iter1 < iter2 
iter1 > iter2 
iter1 <= iter2
iter1 >= iter2

------------------------------------------------------
## Fornire un semplice esempio di Violazione del principo DIP (Dependency Inversion Principle), spiegando brevemente in cosa consiste la violazione.

Il Dependency Inversion Principle (DIP) è uno dei principi SOLID per la programmazione orientata agli oggetti. In sintesi, consiste nel invertire la dipendenza tra le classi alte e quelle basse, in modo che le classi alte non dipendano dalle classi basse, ma dipendano da interfacce astratte.


------------------------------------------------------
## Spiegare brevemente la differenza tra istanziazione e la specializzazione di un template di funzione.

La "**istanziazione**" di un template di funzione si verifica quando il compilatore genera il codice effettivo per una specifica istanza della funzione. Ciò avviene quando la funzione viene chiamata con un certo tipo di dati o quando il compilatore verifica la compatibilità del tipo in una determinata posizione del codice.



La "**specializzazione**" di un template di funzione è la creazione di una versione specifica della funzione per un certo tipo di dati. Ciò può essere fatto usando una sintassi speciale per creare una specializzazione esplicita della funzione. Questo consente di fornire una implementazione personalizzata per un certo tipo di dati, anziché utilizzare l'implementazione generica per tutti i tipi di dati.



Ad esempio, si potrebbe avere una funzione di template di stampa generica:

````
template <typename T>
void print(T value) {
    std::cout << value << std::endl;
}

//che può essere istanziata in più punti nel codice

print(5);   // istanza per int
print(3.14);  // istanza per double

//Ma potrebbe essere specializzato per un certo tipo di dati
print<int>(int value) //specializzazione
````

------------------------------------------------------
## Per ogni tipologia di conversione implicita dell’elenco, fornire un esempio minimo di codice (una dichiarazione di funzione e una corrispondente chiamata, come nell’esempio mostrato per la prima riga dell’elenco) che ne causi l’utilizzo.
Nota bene: si richiede di dichiarare opportunamente non solo i parametri delle funzioni, ma anche eventuali tipi utente e variabili usate come argomenti delle chiamate.

| Tipologia conversione         | Dichiarazione            | Chiamata             |
| ----------------------------- | ------------------------ | -------------------- |
| corrispondenza perfetta       | void foo1(int)           | foo1(42);             |
| lvalue ! rvalue . . . . . .   | void foo2(std::string s) | string s="hello"; foo2(s);      |
| decadimento di array          | void foo3(int* x)        | foo3(int a[2]={1,2}): |
| conversione di qualificazione | void foo4(const int& x)  | x=42; foo4(x);       |
| promozione intera             | void foo5(int x)         | foo5( "c");          |
| promozione floating point     | void foo6(float x)       | foo6("c");           |
| conversione standard          | void foo7 (char c)       | foo7(4);             |
| conversione utente            | void foo8(razionale x)   | foo8(int x);          |

------------------------------------------------------
 ## Le conversioni implicite del C++ sono distinte nelle categorie: corrispondenze esatte (E), promozioni (P), conversioni standard (S), conversioni definite dall’utente (U). Per ognuna delle categorie suddette scrivere una dichiarazione di funzione (con un solo parametro formale) e una corrispondente chiamata di funzione che generi una conversione implicita della categoria considerata.
<br>
corrispondenza esatta:
void foo(int x);
foo(2);
<br>
Promozione:
void foo(int x);
foo('c');
<br>
conversione std:
void foo(char c);
foo(12);
<br>
conversione definita dall'utente:
struct Razionale {int n; int d};
void foo (int x);
Razionale r(1,2);
foo(r;)
<br>
------------------------------------------------------
## Fornire un semplice esempio di violazione della ODR (regola della definizione unica) che, tipicamente, non viene rilevato dal compilatore (in senso stretto, cioe prima della fase di collegamento).


La violazione ODR del 4 tipo non genera errore, cioè il linker non è tenuto a farlo.
Un esempio sarebbe:
````
----- file1.cc
struct S { int a; int b; }; // definizione del tipo S
S s;   
----- file2.cc
struct S { int b; int a; }; // definizione del tipo S (sintassi diversa)
S s;         
````
------------------------------------------------------
## Fornire un semplice esempio di violazione della ODR (regola della definizione unica) che possa essere facilmente rilevato dal compilatore (in senso stretto, cioe prima della fase di collegamento, affidata al linker).

la violazione del tipo 2 viene facilmente rilevata , quando una variabile o una funzione viene dichiarata ma mai definita.

------------------------------------------------------
## Le conversioni implicite del C++ sono distinte nelle seguenti categorie: corrispondenze esatte (E), promozioni (P), conversioni standard (S), conversioni definite dall’utente (U). Assumendo che le variabili sc, i, ul, ai abbiano tipo, rispettivamente, signed char, int, unsigned long, int[100], per ognuno degli accoppiamenti argomento/parametro definiti di seguito, indicare (se esiste) la categoria della corrispondente conversione implicita.

![[Pasted image 20230107213040.png]]


| Indice | Conversione |
| ------ | ----------- |
| A      |   S          |
| B      |  E           |
| C      |    S         |
| D      |    decadimento array        |
| E      |    conversione di qualificazione      |
| F      |  decadimento array           |

------------------------------------------------------
