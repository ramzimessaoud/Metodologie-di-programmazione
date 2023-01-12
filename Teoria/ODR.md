(ONE DEFINITION RULE)

La regola della definizione unica (ODR) è una regola presente in molte implementazioni del linguaggio di programmazione C++ che stabilisce come dovrebbero essere definite e utilizzate le entità nel programma. La regola della definizione unica è stata introdotta per evitare ambiguità e incongruenze nei programmi C++.

La ODR (regola della definizione unica) stabilisce quanto segue (nota: si fornisce una versione semplificata, omettendo casi speciali):

 1) ogni unità di traduzione che forma un programma può contenere non più di una definizione di una data variabile, funzione, classe, enumerazione o template;

 2) ogni programma deve contenere esattamente una definizione di ogni variabile e di ogni funzione non-inline usate nel programma;

 3) ogni funzione inline deve essere definita in ogni unità di traduzione che la utilizza;

 4) in un programma vi possono essere più definizioni di una classe, enumerazione, funzione inline, template di classe e template di funzione (in unità di traduzione diverse, in virtù del punto 1) a condizione che:
a) tali definizioni siano sintatticamente identiche;
b) tali definizioni siano semanticamente identiche.    



VIOLAZIONI ODR:

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
Il linker, al momento di collegare (l'object file prodotto dalla compilazione di) file3.cc con il resto del programma potrebbe indifferentemente invocare la funzione foo definita in file1.cc o quella definita in file2.cc, ottenendo effetti non prevedibili. Tipicamente, il linker segnalerà l'errore, ma in realtà le regole del linguaggio dicono che non è tenuto a farlo (la formula usata nello standard è "no diagnostic required") e, nel caso, la colpa dell'errore ricade sul programmatore.
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
