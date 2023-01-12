Mostrare il processo di risoluzione dell’overloading per le seguenti chiamate di funzione. Per ogni  
chiamata, indicare l’insieme delle funzioni candidate, l’insieme delle funzioni utilizzabili e, se esiste,  
la migliore funzione utilizzabile.
![[Pasted image 20221216130419.png]]


le chiamate saranno converite in lettere:

A) candidate {1,2}  utilizzabili {1,2} migliore{2} match perfetto
B) candidate {5} utilizzabili {5}  migliore {5}
C) candidate {3,6} utilizzabili {3,6}  migliore {AMBIGUA}
D) candidate {4,7} utilizzabili {4}  migliore {4}
E) candidate {1,2,5,8} utilizzabili {1,2,5}  migliore {5}
F)  candidate {} utilizzabili {}  migliore {}
G) candidate {3} utilizzabili {3}  migliore {3}
H) candidate {3,6} utilizzabili {3,6}  migliore {ambigua}

------------------------------------------------------
Indicare l’output prodotto dal seguente programma:
![[Pasted image 20221216133643.png]]

Constructor Base::Base()
Constructor Base::Base()
Constructor Derived::Derived()
=== 1 ===
Base::f(int)
Base::f(int)
Base::g(int)
=== 2 ===
Derived::f(char)
Derived::f(char)
Derived::g(int)
=== 3 ===
//Base::f(int) sbagliato
Base::f(double)
Base::f(int)
Derived::g(int)

Destructor Derived::~Derived()
Destructor Base::~Base()
Destructor Base::~Base()

Elencare le clausole della regola della definizione unica (ODR – One Definition Rule) e, per ognuna di esse, fornire un esempio di violazione della regola.



