I template sono una caratteristica dei linguaggi di programmazione che consente di creare funzioni o classi che possono essere utilizzate con una varietà di tipi di dati diversi. 

Ecco alcune **regole importanti** da tenere presente quando si lavora con i template:

I template non sono funzioni o classi concrete, ma schemi di funzioni o classi da cui possono essere derivate diverse funzioni o classi. Quando si utilizza un template per creare una funzione o una classe concreta, si dice che si sta "istanziando" il template.

Quando si istanzia un template, si ottiene una funzione o una classe concreta che è diversa dal template originale. Questa funzione o classe concreta deve essere definita come qualsiasi altra funzione o classe.

La Sfinae (Substitution Failure Is Not An Error) è una tecnica utilizzata per risolvere gli errori di sostituzione dei parametri nei template. Se un template non può essere sostituito correttamente, questo non viene considerato un errore.

In caso di ambiguità, si escludono le funzioni ottenute tramite istanziazione (quelle templatiche) a favore delle funzioni non templatiche. Ad esempio, se si hanno due funzioni, una templatica e una non templatica, viene scelta quella non templatica.

Se si hanno due funzioni templatiche, si sceglie quella più specializzata, ovvero quella il cui insieme delle istanze possibili è un sottoinsieme di quello dell'altra funzione. Ad esempio, se si hanno due funzioni templatiche f come segue:


template <typename T>
void f(T t1, T t2); // funzione 1

template <typename T, typename U>
void f(T t, U u); // funzione 2

La funzione 1 è più specializzata perché l'insieme delle possibili istanziazioni della funzione 2 comprende sia quelle in cui T è uguale a U (che rappresentano l'insieme delle istanziazioni della funzione 1) che quelle in cui T è diverso da U. L'insieme delle istanziazioni della funzione 2 include quindi l'insieme delle istanziazioni della funzione 1, quindi la funzione 1 è più specializzata.


esempio: 
````
Non ci sono namespace quindi è più semplice

void f(const char* s); //funzione 1
template <typename T>
void f(T t); //funzione 2
template <typename T>
void f(T t1, T t2); //funzione 3
template <typename T, typename U>
void f(T t, U u); //funzione 4
template <typename T>
void f(T* pt, T t); //funzione 5
template <typename T, typename U>
void f(T* pt, U u); //funzione 6
template <typename T>
void g(T t, double d); //funzione 7
template <typename T>
void g(T t1, T t2); //funzione 8
int test() {
f(’a’); // chiamata #1
f("aaa"); // chiamata #2
int i;
f(i); // chiamata #3
f(i, i); // chiamata #4
f(i, &i); // chiamata #5
f(&i, i); // chiamata #6
double d;
f(i, d); // chiamata #7
f(&d, i); // chiamata #8
long l;
g(l, i); // chiamata #9
g(l, l) ; // chiamata #10
g(l, d) ; // chiamata #11
g(d, d) ; // chiamata #12
````
chiamata 1: candidate {1, 2, 3, 4, 5, 6} utilizzabili {1, 2} migliore {1} (viene selezionata la funzione #1 perché è la più specifica)

chiamata 2: candidate {1, 2, 3, 4, 5, 6} utilizzabili {1, 2} migliore {1} (viene selezionata la funzione #1 perché è la più specifica)

chiamata 3: candidate {2, 3, 4, 5, 6} utilizzabili {2} migliore {2} (viene selezionata la funzione #2 perché è l'unica disponibile)

chiamata 4: candidate {3, 4, 5, 6} utilizzabili {3} migliore {3} (viene selezionata la funzione #3 perché è l'unica disponibile)

chiamata 5: candidate {4, 5, 6} utilizzabili {4} migliore {4} (viene selezionata la funzione #4 perché è l'unica disponibile)

chiamata 6: candidate {4, 5, 6} utilizzabili {4, 5, 6} migliore {5} (viene selezionata la funzione #5 perché è la più specifica)

chiamata 7: candidate {4, 5, 6} utilizzabili {4, 5, 6} migliore {6} (viene selezionata la funzione #6 perché è la più specifica)

chiamata 8: candidate {4, 5, 6} utilizzabili {4, 5, 6} migliore {6} (viene selezionata la funzione #6 perché è la più specifica)

chiamata 9: candidate {7, 8} utilizzabili {7} migliore {7} (viene selezionata la funzione #7 perché è l'unica disponibile)

chiamata 10: candidate {8} utilizzabili {8} migliore {8} (viene selezionata la funzione #8 perché è l'unica disponibile)

chiamata 11: candidate {7, 8} utilizzabili {7} migliore {7} (viene selezionata la funzione #7 perché è l'unica disponibile)

chiamata 12: candidate {7, 8} utilizzabili {7} migliore {7} (viene selezionata la funzione #7 perché è l'unica disponibile)

