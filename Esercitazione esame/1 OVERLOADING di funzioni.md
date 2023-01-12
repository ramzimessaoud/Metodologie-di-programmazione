RISOLUZIONE OVERLOADING (per ogni singola chiamata)  
1) Funzioni candidate: il compilatore decide quali sono le funzioni candidate alla chiamata.  
2) Funzioni utilizzabili (viable): alcune candidate potrebbero essere buone o no; esclude quelle non  
utilizzabili.  
3) Funzione migliore utilizzabile: si seleziona la migliore (se esiste).


###### 1) Candidate:
si guarda  
- il nome.  
- visibilità: deve essere visibile nel punto in cui noi la chiamiamo.  
- Non mi interessano i tipi o il numero dei valori passati per essere candidatata

**ADL (Argument Dependent Lookup)**: se nella chiamata negli argomenti esiste un tipo di dato 
definito dall'utente all'interno del namespace “Pippo” allora in automatico il namespace “Pippo” si  apre e mostra tutte le funzioni contenute. In caso contrario il namespace non si apre.
```
namespace A {  
void foo();  
class R{};  
}  
namespace B {  
void foo();  
}  
void foo();  
R var; //apre il namespace A  
foo(var); //come candidata ho anche il foo dentro ad A  
```
- Posso disabilitare ADL ad esempio:  
`B::foo(var); //così la A non si apre  

- Mi possono fregare quando:  
```
namespace A {  
void foo(int);  
namespace B {  
void foo(double);  
void foo(float);  
foo(5.3); //5.3 è un DOUBLE di default  
//questa foo appena trova le due foo float e double si ferma e quindi non vede la foo int; questo  
perché il namespace applica un hiding (nasconde i nomi delle altre funzioni)  
} //fine namespace B  
} //fine namespace A
```
Altro esempio:
```
class B {  
void foo(double);  
};  
class D : public B {  
void foo(int);  
}  
D d;  
d.foo(5.3); //vede la foo di D e basta
```

###### 2) Utilizzabili: si guarda  
- Numero e tipo argomenti: troppi non va bene, pochi dipende se sono stabiliti dei valori di default  
- Si considerano le conversioni implicite per capire il numero e tipo degli argomenti

###### 3) Migliore: si guarda  
- Ne esiste una sola migliore (vince quella).  
- Non ne esiste una migliore (ambigua).  

**Ambiguità**: si fanno n confronti, uno per ogni argomento di una funzioni e vince la funzione che in  
tutti gli altri confronti non ha mai perso e ha vinto almeno una volta.  
Per risolvere le ambiguità si utilizzano le conversioni; di seguito sono indicate in ordine dalla  
migliore alla peggiore, le:
**Conversioni**  
**0) Nessuna conversione.  
1) [[Corrispondenze esatte]] (match perfetto) 
2) [[Corrispondenza per promozione]] 
3) [[Conversione standard]]  
4) [[Conversioni definite dall’utente]]**


altre regole:

- Una funzione const puo prendere chiamate const e modificabili

- Una chiamata const puo essere presa solo da una funzione const;

Base bas;  
Derived der;  
const Base* pbas = &der;  //accede al namescope di base
Derived* pder = &der;  //accede prima al name scope di derived, se non trova nessuna candidata, accede a base per effetto di (HIDING)


La migliore non deve aver perso nessun confronto per poterlo essere.
quindi se ho l'overloading tra 2 funzioni, la prima vince 2 confronti ma perde la 3 contro la seconda , nessuna di essi è migliore.


Public e Private non hanno nessuna influenza sull'overloading delle funzioni.
ma private darebbe un errore di compilazione.