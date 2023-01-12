SOMMARIO:

- LEZIONE 9 
	- TRY-CATCH 
	- RAII 
	- SMART POINTER
	
- CORREZIONE CODICE GESTIONE RISORSE
	- 1 RISORSA
			-regola dei 6
	- 2 RISORSE
		- try catch
		- smart pointer




  Codice utente che vorrebbe lavorare su alcune risorse
  garantendo la corretta interazione con le risorse
  (acquisizione, uso e rilascio) anche in presenza di errori.
  Intuitivamente, si vorrebbe eseguire questa sequenza di operazioni:

    acquisisci risorsa r1
    usa risorsa r1

    acquisisci risorsa r2
    usa risorse r1 e r2
    restituisci risorsa r2

    acquisisci risorsa r3
    usa risorse r1 e r3
    restituisci risorsa r3
    restituisci risorsa r1



Una codifica che NON è corretta in presenza di errori 




# CONTROLLO TRAMITE NULL POINTER SENZA ECCEZIONI (CATTIVA): 
risorsa_no_exc.hh
````
#ifndef GUARDIA_risorsa_no_exc_hh
#define GUARDIA_risorsa_no_exc_hh 1

// Tipo dichiarato ma non definito (per puntatori "opachi")
struct Risorsa;

// Restituisce un puntatore nullo se l'acquisizione fallisce.
Risorsa* acquisisci_risorsa();

// Restituisce true se si è verificato un problema.
bool usa_risorsa(Risorsa* r);

// Restituisce true se si è verificato un problema.
bool usa_risorse(Risorsa* r1, Risorsa* r2);

void restituisci_risorsa(Risorsa* r);

#endif // GUARDIA_risorsa_no_exc_hh
````

user_no_exc.cc
````
#include "risorsa_no_exc.hh"

bool codice_utente() {
  Risorsa* r1 = acquisisci_risorsa();
  if (r1 == nullptr) {
    // errore durante acquisizione di r1: non devo rilasciare nulla
    return true;
  }

  // acquisita r1: devo ricordarmi di rilasciarla

  if (usa_risorsa(r1)) {
    // errore durante l'uso: rilascio r1
    restituisci_risorsa(r1);
    return true;
  }

  Risorsa* r2 = acquisisci_risorsa();
  if (r2 == nullptr) {
    // errore durante acquisizione di r2: rilascio di r1
    restituisci_risorsa(r1);
    return true;
  }

  // acquisita r2: devo ricordarmi di rilasciare r2 e r1

  if (usa_risorse(r1, r2)) {
    // errore durante l'uso: rilascio r2 e r1
    restituisci_risorsa(r2);
    restituisci_risorsa(r1);
    return true;
  }

  // fine uso di r2: la rilascio
  restituisci_risorsa(r2);
  // ho ancora r1: devo ricordarmi di rilasciarla

  Risorsa* r3 = acquisisci_risorsa();
  if (r3 == nullptr) {
    // errore durante acquisizione di r3: rilascio di r1
    restituisci_risorsa(r1);
    return true;
  }

  // acquisita r3: devo ricordarmi di rilasciare r3 e r1

  if (usa_risorse(r1, r3)) {
    // errore durante l'uso: rilascio r3 e r1
    restituisci_risorsa(r3);
    restituisci_risorsa(r1);
    return true;
  }

  // fine uso di r3 e r1: le rilascio
  restituisci_risorsa(r3);
  restituisci_risorsa(r1);

  // Tutto ok: lo segnalo ritornando false
  return false;
}
````

questi codici sono exception safe ma sono scritti molto male, sono difficili da leggere.
quindi dobbiamo trovare un'altra metodologia di scrivere il codice.

nota:
se acquisiamo le risorse, sono la nostra responsabilità, se le rilasciamo sono responsabilità dell'utente.

regole per l'exception safe:
1 dobbiamo rilasciare le risorse che abbiamo acquisito
2 dobbiamo essere neutrali rispetto le eccezioni (segnalazioni di errorr)
	(qua ritorniamo un bolleano, perche non usa eccezioni)
3 se cerchiamo di acquisire uan risorsa, ma falliamo, dobbiamo rilasciare tutte le risorse che abbiamo acquisito.

# CONTROLLO TRAMITE BLOCCHI TRY-CATCH CON ECCEZIONI (MEDIOCRE):

risorsa-exc.hh
````
#ifndef GUARDIA_risorsa_exc_hh
#define GUARDIA_risorsa_exc_hh 1

#include "risorsa_no_exc.hh"

struct exception_acq_risorsa {};
struct exception_uso_risorsa {};

// Lancia una eccezione se non riesce ad acquisire la risorsa.
inline Risorsa*
acquisisci_risorsa_exc() {
  Risorsa* r = acquisisci_risorsa();
  if (r == nullptr)
    throw exception_acq_risorsa();
  return r;
}

// Lancia una eccezione se si è verificato un problema.
inline void
usa_risorsa_exc(Risorsa* r) {
  if (usa_risorsa(r))
    throw exception_uso_risorsa();
}

// Lancia una eccezione se si è verificato un problema.
inline void
usa_risorse_exc(Risorsa* r1, Risorsa* r2) {
  if (usa_risorse(r1, r2))
    throw exception_uso_risorsa();
}

#endif // GUARDIA_risorsa_exc_hh
````

segnalazione errore tramite eccezioni.

user_try_catch.cc
````
#include "risorsa_exc.hh"

void codice_utente() {
  Risorsa* r1 = acquisisci_risorsa_exc();
  try { // blocco try che protegge la risorsa r1
    usa_risorsa_exc(r1);

    Risorsa* r2 = acquisisci_risorsa_exc();
    try { // blocco try che protegge la risorsa r2
      usa_risorse_exc(r1, r2);
      restituisci_risorsa(r2);
    } // fine try che protegge r2
    catch (...) {
      restituisci_risorsa(r2);
      throw;
    }

    Risorsa* r3 = acquisisci_risorsa_exc();
    try { // blocco try che protegge la risorsa r3
      usa_risorse_exc(r1, r3);
      restituisci_risorsa(r3);
    } // fine try che protegge r3
    catch (...) {
      restituisci_risorsa(r3);
      throw;
    }
    restituisci_risorsa(r1);

  } // fine try che protegge r1
  catch (...) {
    restituisci_risorsa(r1);
    throw;
  }

}
````
/*
  ## Osservazioni:

  1) si crea un blocco try/catch per **ogni singola risorsa acquisita**

  2) il blocco si apre subito *dopo* l'acquisizione della risorsa (se l'acquisizione fallisce, non c'è nulla da rilasciare)

  3) lo responsabilità del blocco try/catch è di proteggere *quella* singola risorsa (ignorando le altre)

  4) al termine del blocco try (prima del catch) va effettuata la "normale" restituzione della risorsa (caso NON eccezionale)

  5) **la clausola catch usa "..." per catturare qualunque eccezione**:
     non ci interessa sapere che errore si è verificato (non è nostro compito), dobbiamo solo rilasciare la risorsa protetta

  6) nella clausola catch, dobbiamo fare due operazioni:
      - **rilasciare la risorsa protetta**
      - **rilanciare l'eccezione catturata** (senza modificarla) usando l'istruzione "**throw**;"

  Il rilancio dell'eccezione catturata (seconda parte del punto 6) garantisce la "neutralità rispetto alle eccezioni": i blocchi catch catturano le eccezioni solo temporaneamente, lasciandole poi proseguire. In questo modo anche gli altri blocchi catch potranno fare i loro rilasci di risorse e l'utente otterrà comunque l'eccezione, con le informazioni annesse, potendo quindi decidere come "gestirla".

ERRORI:
se il try si mette prima di acquisire la risorsa:
try{
acquisici_risorsa();
usa_risorsa():
rilascia();
} catch(...) {
rilascia();
throw;
}

in caso di eccezione provandola ad acquisire si va a rilasciare una risorsa mai acquistia nel blocco catch.


IDIOMA RAII-RRID: 

file user_raii.cc
````
#include "risorsa_raii.hh"

void codice_utente() {
  Gestore_Risorsa r1;
  usa_risorsa_exc(r1.get());
  {
    Gestore_Risorsa r2;
    usa_risorse_exc(r1.get(), r2.get());
  }
  Gestore_Risorsa r3;
  usa_risorse_exc(r1.get(), r3.get());
}
````
per gestire la risorse creo un "Gestore" che la gestisce o lancia l'eccezione
non si rilascia la risorsa perche viene rilasciata automaticamente all'interno del distruttore


file risorsa_raii.hh
````
#ifndef GUARDIA_risorsa_raii_hh
#define GUARDIA_risorsa_raii_hh 1

#include "risorsa_exc.hh"

// classe RAII-RRID (spesso detta solo RAII, per brevità)
// RAII: Resource Acquisition Is Initialization
// RRID: Resource Release Is Destruction

class Gestore_Risorsa {
private:
  Risorsa* res_ptr;
public:
  // Costruttore: acquisisce la risorsa (RAII)
  Gestore_Risorsa() : res_ptr(acquisisci_risorsa_exc()) { }

  // Distruttore: rilascia la risorsa (RRID)
  ~Gestore_Risorsa() {
    // Nota: si assume che restituisci_risorsa si comporti correttamente
    // quando l'argimento è il puntatore nullo; se questo non è il caso,
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

1. Si acquisisce la risorsa tramite il costruttore 
2. si rilascia la risorsa all'interno del distruttore
3. per gestire la risorse creo un "Gestore" che la gestisce o lancia l'eccezione
4. uso un puntatore per lavorare sulla risorsa (res_ptr)
5. Il compilatore mi fornisce di default anche il costruttore per copia, per spostamento e assegnamento per copia e per spostamento, che non sono corretti perche rovinano la nostra gestione delle risorse, dobbiamo impedire che il compilatore ce li crea di default tramite la scritta "delete" che dice al compilatore di disabilitarli.
	  // Disabilitazione delle copie
	 ``Gestore_Risorsa(const Gestore_Risorsa&) = delete;
	``Gestore_Risorsa& operator=(const Gestore_Risorsa&) = delete;
posso implementare il costruttore per spostamento per "spostare la gestione" della risorsa passando l'indirizzo a un puntatore dato:
    ````// Costruzione per spostamento (C++11)
  Gestore_Risorsa(Gestore_Risorsa&& y)
    : res_ptr(y.res_ptr) {
    y.res_ptr = nullptr;
  } ````
stessa cosa per l'assegnamento per spostamento tramite l'operatore =, rilascio la risorsa che sto usando e passo l'indirzzo puntato a un puntatore dato:
````
  // Assegnamento per spostamento (C++11)
  Gestore_Risorsa& operator=(Gestore_Risorsa&& y) {
    restituisci_risorsa(res_ptr);
    res_ptr = y.res_ptr;
    y.res_ptr = nullptr;
    return *this;
  }
  ````


Riepilogo:
Ecco alcuni punti chiave da considerare quando si scrive una classe gestore di risorse utilizzando l'idioma RAII:

1.  Acquisire la risorsa al momento della creazione dell'oggetto gestore di risorse: questo può essere fatto nel costruttore della classe gestore di risorse.
    
2.  Rilasciare la risorsa al momento della distruzione dell'oggetto gestore di risorse: questo può essere fatto nel distruttore della classe gestore di risorse.
    
3.  Evitare la copia della classe gestore di risorse: è spesso necessario impedire la copia di un gestore di risorse, poiché ciascun gestore di risorse dovrebbe gestire una risorsa univoca. Ciò può essere fatto eliminando le funzioni di copia (costruttore di copia e operatore di assegnazione) impostando il loro tipo di ritorno come `delete`.
    
4.  Consentire lo spostamento della classe gestore di risorse: è spesso utile consentire lo spostamento di un gestore di risorse, in modo da poter spostare la gestione di una risorsa da una posizione all'altra senza copiarla. Ciò può essere fatto implementando un costruttore di spostamento e un operatore di spostamento.
    
5.  Fornire un modo per ottenere l'accesso alla risorsa gestita: spesso è necessario fornire un modo per ottenere l'accesso alla risorsa gestita da una classe gestore di risorse, ad esempio per passarla a una funzione che ne ha bisogno. Ciò può essere fatto mediante una funzione membro, come `get`, che restituisce il puntatore alla risorsa.
    

Tenendo presenti questi punti chiave, è possibile scrivere una classe gestore di risorse utilizzando l'idioma RAII per gestire le risorse in modo affidabile e sicuro.



# COREZIONE CODICE GESTIONE RISORSE

1 RISORSA:

 ````
class T {

private:
int a
int* pi;

public:

//costruttore 
T()

//distruttore
~T()

//costruttore di copia
T(cosnt T& other)

//assegnamento
T& operator=(const T& other)

//spostamento
T(T&& other)

T& operator(T&& other)

}
 ````

quando creaimo una classe abbiamo di default questi 6 metodi generati in automatico dal compilatore, ma se stiamo gestendo delle risorse queste possono crearci dei problemi, quindi dobbiamo implementarle o dissativarle:

il costruttore 

 ````
class T {

private:
int a
int* pi;

public:

//costruttore dovra creare la risorsa con la new
T() : p (new int()) {}

//distruttore dovra fare la delete sulla risorsa per rilascairla
~T() { delete pi; }

//costruttore di copia dovra essere disabilittato tramite delete o mettendolo private oppure lo implementiamo come "DEEP COPY" copia profonda, cioè che creerà una copia di other ma allocando una nuova risorsa identica a quelladi this.
//deep copy
T(cosnt T& other) : a(t.a), p(new int(*t.p)) 

//assegnamento lo disattiviamo oppure eleminiamo la memory allocata precedentemente e la allochiamo per this
T& operator=(const T& other){
delete p
a=t.a
p=new int(t.p)
return *this;
}

//spostamento treasferiamo la risorsa cancellando quella precedente.
T(T&& other) : a(t.a), p(t.p) {t.p = nullptr; }

T& operator(T&& other){
delete p
a=t.a
p=new int(t.p)
return *this;
}



}
 ````


2 RISORSE:


- Esercizio 1
QUANDO ABBIAMO 2 RISORSE LE COSE SI COMPLICANO, DATO CHE POTREBBE FALLIRE L'ACQUISIZIONE A UNA DELLE DUE RISORSE CAUSANDO PROBLEMI.


````
#include <string>
Class A {
private:
int* pi;
std::string str
double* pd;

public:
A(const std::string& s)
	: pi(new int), str(s), pd(new double) {}

~A() {
delete pi;
delete pd;
}

}
````

qui abbiamo diversi problemi che andremo a risolvere:
il primo problema è quando acquisiamo pi con la new int e la acquisizione delle pd con la new double fallisce lanciando eccezione, uscendo in modalità eccezionale, non invochiamo il distruttore facendo memory leak, pi non viene mai rilasciata.

una soluzione sarebbe usare un blocco  try_catch
````
public:
A(const std::string& s)
	: pi(new int), str(s), pd(nullptr) { // pd lo invochiamo senza risora
	try { //proteggiamo pi
		pd= new double; //allochiamo pd
		}catch(...){ //se falisce restituiamo pi e lanciamo eccezione.
		delete pi;
		throw;
		}
	}
	}
````

un secondo problema è la regola dei 6, cioè disabilittare i costruttore di copia, assegnamento e spostamento.

````
#include <string>
Class A {
private:
int* pi;
std::string str
double* pd;
A(const A& other); //privato non implementato
A& operator=(const A& other); //privato non implementato


public:
A(const std::string& s)
	: pi(new int), str(s), pd(nullptr) { // pd lo invochiamo senza risora
	try { //proteggiamo pi
		pd= new double; //allochiamo pd
		}catch(...){ //se falisce restituiamo pi e lanciamo eccezione.
		delete pi;
		throw;
		}
	}
	}
````


CON SMART POINTER:
Abbiamo utilizzato i blocchi try catch per proteggere la risorsa ma possiamo usare gli smart pointer che è una soluzione più efficente.

usando gli smart pointer possiamo anche evitare di disabilitare il costruttore di copia,assegnamento, spostamento e le delete nel distruttore dato che ci penserà autmaticamente lo smart pointer.

in questo modo posso allocare 2 o più memorie senza preocuparmi che una di esse falisce.

````
#include <string>
Class A {
private:
	std::unique_ptr<int> pi;
	std::string str
	std::unique_ptr<double> pd;

public:

A(const std::string& s)
	: pi(new int), str(s), pd(new double) {}
	
~A() {}= default;
	
}
````



- Esercizio 2
Discutere brevemente se, è sotto quali condizioni le seguenti classi si comportano corretamente dal punto di vista dell'allocazione delle risorse (si noti che quella mostrata è da considerarsi l'implementazione completa delle classi in questione)

````
#include <vector>

template <typename T>
Class A{
private:
	int i
	T* p;
	double d;


public:
A() :i(), p(new t), d() {}
~A() {delete p; };

};




template <typename T>
Class B{
private:
	int i
	T* p;
	double d;
//privati non implementati
B(consst B&);
B& operator=(const B&);

public:
B() :i(100), p(new t[i]), d() {}
~B() {delete[] p; };

};


class C : private A<std::vector<int>>
private: 
B<double> b1; //ci pensa la classe B a gestire le risorse
B<int> b2; //ci pensa la classe B a gestire le risorse

//privati non implementati
C(consst C&);
C& operator=(const C&);


public:
	C() {}
	};

````

La classe A si comporta male se l'utente prova a fare copie, assegnazione o spostamenti, dato che non essendo implementate, vengono fornite automaticanete dal compilatore e dato che allochiamo memoria, la copia o una di queste altre operazioni andra a causare probelmi.

Soluzione: disabbilitiamo questi costruttori/operatori, mettendoci la dicitura delete(c++11) o mettendoli private, oppure possiamo implementarli noi corretamente.

La classe B si comporta bene se siamo in c++11 dato che avendo privatizzato il cosstruttore di copia e di assegnamento, il compilatore implicitamente non ci fornira quelli di spostamento, altrimenti in una versione più vecchia dovremmo disabilitare anche essi.

La classe C utilizza delle risorse di B, che quindi sono gia gestite corretamente all'interno della classe B, disabilitta anche la copia e assegnamento e spostamento.
è una sottoclasse di A che è dichiarata private, quindi l'utente non potra accedervi.


