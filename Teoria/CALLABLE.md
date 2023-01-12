In C++, i callable sono oggetti o funzioni che possono essere chiamati come se fossero delle funzioni, utilizzando il simbolo `()`. Ciò include funzioni, funzioni membro, funzioni lambda, funzioni di invocazione di classe e oggetti funzione.

(i tipi "chiamabili", cioè "invocabili" come le funzioni):

  * puntatori a funzione
  * oggetti funzione
  * expressioni lambda (dal C++11)

#### I puntatori a funzione
Per esempio, quando usiamo il nome della funzione

  bool pari(int i);

per istanziare il predicato unario della std::find_if, il parametro typename UnaryPred viene legato al tipo concreto "`bool (*)(int)`" (puntatore ad una funzione che prende un argomento intero per valore e restituisce un bool).


#### Gli "oggetti funzione"
una classe che fornisca una definizione (o anche più definizioni, in overloading) del metodo "`operator()`" consente ai suoi oggetti di essere utilizzati al posto delle vere funzioni nella sintassi della chiamata di funzione.

Esempio:

```
struct Pari {
  bool operator()(int i) const {
    return i % 2 == 0;
  }
};

int foo() {
  Pari pari;
  if (pari(12345)) ...
  ...
}
```

Vantaggio:
Efficenza rispetto alle normali funzioni, forniamo al compilatore più oppurtunità per ottimizare il codice.

#### Le espressioni lambda

sono funzioni anonime che possono essere utilizzate per creare una funzione on-the-fly. Sono definite utilizzando l'operatore di definizione di funzione lambda (`[]`) seguito da una lista di parametri e il corpo della funzione racchiuso tra parentesi graffe. Ad esempio:

```
int main() {
   // definizione di una funzione lambda che prende due interi e restituisce il loro prodotto
   auto multiply = [](int x, int y) { return x * y; };
   std::cout << multiply(10, 20) << std::endl; // stampa 200
}
```

**Esempio**: istanziazione di std::find_if con una lambda expression che implementa il predicato "pari" sul tipo T
```
void foo(const std::vector<long>& v) {
  auto iter = std::find_if(v.begin(), v.end(), [](const long& i) {
    return i % 2 == 0;
});
  // ... usa iter
}
```

**L'espressione lambda è data dalla sintassi**

  `[](const long& i) { return i % 2 == 0; }`

dove si distiguono i seguenti elementi:

`[]`                       --> capture list (lista delle catture)
`(const long& i)`          --> lista dei parametri (opzionale)
`{ return i % 2 == 0; }`   --> corpo della funzione

In questo esempio, la lista delle catture è vuota; inoltre, il tipo di ritorno è omesso, in quanto viene dedotto dall'istruzione di return contenuta nel corpo della funzione.
Volendo (ma di solito non si fa), è possibile specificarlo con la sintassi del "trailing return type", che usa l'operatore freccia:

  `[](const long& i) -> bool { return i % 2 == 0; }`

Questo uso della espressione lambra all'interno della invocazione della std::find_if corrisponde intuitivamente alle seguenti operazioni:

1) definizione di una classe "anonima" per oggetti funzione, cioè una classe dotata di un nome univoco scelto dal sistema;
2) definizione all'interno della classe di un metodo operator() che ha i parametri, il corpo e il tipo di ritorno specificati (o dedotti) dalla lambda expression;
3) creazione di un oggetto funzione "anonimo", avente il tipo della classe anonima suddetta, da passare alla std::find_if.

In pratica, è come se il programmatore avesse scritto:
```
  struct Nome_Univoco {
    bool operator()(const long& i) const { return i % 2 == 0; }
  };

  auto iter = std::find_if(v.begin(), v.end(), Nome_Univoco());
  ```

Le espressioni lambda possono utilizzare il concetto di "**catture**" per accedere a variabili definite al di fuori del corpo della funzione lambda. Ci sono due tipi di catture: catture per valore e catture per riferimento. Nelle catture per valore, una copia delle variabili viene creata all'interno della funzione lambda, mentre nelle catture per riferimento viene creato un riferimento alla variabile originale. È possibile utilizzare la parola chiave `mutable` per indicare che il corpo della funzione può modificare le catture per valore.


Esistono notazioni abbreviate per le "catture implicite":

 [=]   --> cattura (implicitamente) ogni variabile locale usata nel corpo per valore
 [&]   --> cattura (implicitamente) ogni variabile locale usata nel corpo per riferimento
 [=, &pippo, &pluto]  --> cattura (implicitamente) per valore, tranne pippo e pluto che sono catturate per riferimento
 [&, =pippo, =pluto]  --> cattura (implicitamente) per riferimento, tranne pippo e pluto che sono catturate per valore

Il consiglio è di effettuare sempre catture esplicite, per maggiore leggibilità del codice.