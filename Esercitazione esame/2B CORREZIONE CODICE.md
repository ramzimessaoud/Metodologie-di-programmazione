1) Progettazione di un tipo concreto
	- ASSERZIONI
	- CONST CORRECTNESS 
	- Template
	- Passaggi per riferimenti 
	- ostream fuori dalla classe che prende un elemento classe

2) Object oriented
- VIRTUAL
- DEFINIZIONE PURA
- SOLID
- ADATTATORI



## 1 PROGETTAZIONE DI UN TIPO CONCRETTO


Errori di progettazione:
**REGOLA 1° CONST CORRECTNESS:**
Mancanza di Const (costanti) const correctness
mancanza di & per passaggio a riferimento 
costruttore di copia che causa loop chiamando se stesso
stream (ostream,istream)
interni alla classe
esterni senza template e senza attributo della classe per concatenare

`template <t>`
`std::ostream& operator<<(std::ostream& os, const Set<t>& x );`
non hanno dei metodi get interni alla classe
non passa per riferimento `(std::ostream& operator<<)`
manca il const correctness  s`td::ostream& operator<<(std::ostream os) const;`
`virtual ~Set() -> ~Set()`
Conversioni implicite dei costruttori a un parametro (inserire scritta explict )

**REGOLA 2° INVARIANTE DI CLASSE:**
rottura invariante di classe
Precondizione e Postcondizione (programmazione a contratto)
inclusione delle guardie per le ripetizioni
accesso da parte dell’utente alle librerie pubbliche per mancanza di private

Prima cosa da fare è controllare funzione per funzione e inserire la Const correctness e il passaggio per riferimento dove richiesto, dopo di che cercare tutte le possibili rotture e invalidazione dell’invariante di classe.



#### 2) OBJECT ORIENTED:

Ci sono 2 tipi di interfaccie
- astratte
- concrette

quelle concrette ed il codice utente devono dipendere da quella astratta è non viceversa

Nell'interfaccia astratta devono essere presenti tutti i metodi di quelle concrette definite pure con la scrittua virtual che poi verrano fatte l'ovveride all'interno di quelle concrette.

nelle interfaccie concrette devono includere solo l'interfaccia astratta e riscrivere i metodi virtual aggiungendo la scritta ovveride.

il codice utente deve includere solo l'interfaccia astratta.
i metodi chiamati per puntatore in modo che venga fatto il dispatching è chiamati i metodi riscritti nelle interfacce concrette.

in questo modo invertiamo le cattive dipendenze risolvendo le violazioni DIP del principio SOLID.

ESEMPIO:
Uno strumento software utilizza codice come il seguente allo scopo di gestire la produzione automatica di documentazione secondo diversi formati di stampa.

```
#include <string>
using std::string;
class Manual_Generator {
public:
virtual void put(const string& s) = 0;
virtual void set_boldface() = 0;
virtual void reset_boldface() = 0;
// ...
};
```
````
class HTML_Generator : public Manual_Generator {
public:
void put(const string& s);
void set_boldface();
void reset_boldface();
void hyperlink(const string& uri, const string& text);
// ...
};
````

````
class ASCII_Generator : public Manual_Generator {
public:
void put(const string& s);
void set_boldface();
void reset_boldface();
void page_break();
// ...
};

````
````
void f(Manual_Generator* mg_p) {
// ...
HTML_Generator* html_p = dynamic_cast<HTML_Generator*>(mg_p);
if (html_p)
html_p->hyperlink("http://www.cs.unipr.it/ppl", "PPL");
else
mg_p->put("PPL (http://www.cs.unipr.it/ppl)");
// ...
ASCII_Generator* ascii_p = dynamic_cast<ASCII_Generator*>(mg_p);
if (ascii_p)
ascii_p->page_break();
else
// Nota: usare il tag HR per simulare il cambio pagina in HTML.
mg_p->put("<HR>");
// ...
}
````
Enunciare il principio della programmazione orientata agli oggetti che viene violato nel codice
precedente e mostrare (brevemente) le conseguenze di questa violazione. Fornire una soluzione alternativa che rispetti il principio in questione, mostrando le modifiche da apportare al codice.

SOLUZIONE:

questo codice ha cattive dipendenze quindi viola il principio SOLID DIP.

```
#include <string>
using std::string;
class Manual_Generator {
public:
virtual void put(const string& s) = 0;
virtual void set_boldface() = 0;
virtual void reset_boldface() = 0;

//aggiungo metodi mancanti, puri e virtuali

virtual void hyperlink(const string& uri, const string& text) = 0; 
virtual void page_break() =0 ;

// ...
};
```
````
class HTML_Generator : public Manual_Generator {
public:
void put(const string& s);
void set_boldface();
void reset_boldface();
void hyperlink(const string& uri, const string& text) ovveride {
	put("PPL (http://www.cs.unipr.it/ppl)");
}
// ...
};
````

````
class ASCII_Generator : public Manual_Generator {
public:
void put(const string& s);
void set_boldface();
void reset_boldface();
void page_break() ovveride { 
	put("<HR>"); };
// ...
};

````
````

#include "Manual_Generator.hh"

void f(Manual_Generator* mg_p) {

mg_p->hyperlink("http://www.cs.unipr.it/ppl", "PPL");

mg_p->page_break();


}
````

abbiamo semplificato il codice utente invertendo le dipendenze e implementando corretamente le interfacce.



#### ADATTATORI
Una ditta di sviluppo software si occupa della manutenzione di una applicazione che, per implementare alcune sue funzionalita, utilizza la libreria “BiblioSoft”, fornita da terzi e per la quale non si ha a disposizione il codice sorgente. In particolare, l’applicazione accede alla classe BSoft, la cui interfaccia e la seguente:
````
class BSoft {
private:
// ...
public:
void s1();
void s2(const BSoft& y, int n);
void s3(int n);
// ...
};
````

L’utilizzo che ne viene fatto all’interno dell’applicazione e tipicamente il seguente:

````
void f(BSoft& x, const BSoft& y, int n) {
// ...
if (n > 0)
x.s3(n);
else {
x.s1();
n = 5;
}
// ...
x.s2(y, n);
// ...
}
````

Sul mercato esiste un’altra libreria “BiblioWare” che offre utilita simili e che e preferita da alcuni degli utenti della ditta. L’interfaccia in questo caso e la seguente:

````
class BWare {
private:
// ...
public:
void w1();
void w2_1(const BWare& y);
void w2_2(int n);
void w3(int n);
// ... ecc.
};
````

L’invocazione dei metodi BWare::w1() e BWare::w3(n) ha lo stesso effetto dell’invocazione di BSoft::s1() e BSoft::s3(n), rispettivamente. I metodi BWare::w2_1(x) e BWare::w2_2(n), se invocati immediatamente uno dopo l’altro (sullo stesso oggetto), hanno lo stesso effetto dell’invocazione di BSoft::s2(x, n).
Mostrare come puo essere integrato/modificato il codice precedente al fine di rendere agevole l’utilizzo di una qualunque delle due librerie. 
(**Nota Bene: non e possibile modificare il codice di BSoft e BWare**).
Quale principio della programmazione orientata agli oggetti era violato nella codifica originale
dell’applicazione?


SOLUZIONE:

dato che il codice non è modificabile andremo a costruire una interfaccia astratta tramite una classe adattatore che includa i metodi necessari di Bsoft e Bware per il codice utente.

````
// file Biblio_Adapt.hh

Class Biblio_Adapt {
private:
// ...

public:

virtual void B1() {
	Bware::w1()
}
virtual void B2(Biblio_Adapt& x, int n) {
	Bsoft::s2(x,n)
}
virtual void B3(int n){
	Bware::w1(n)
}
virtual ~Biblio_Adapt () {};
};


#include "Biblio_Adapt.hh"
void f(Biblio_Adapt& x, const Biblio_Adapt& y, int n) {
// ...
if (n > 0)
x.b3(n);
else {
x.b1();
n = 5;
}
// ...
x.b2(y, n);
// ...
}
````