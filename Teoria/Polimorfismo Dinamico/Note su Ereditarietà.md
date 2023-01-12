
[[Ereditarietà Multipla]]
quando andiamo a derivare le classi ricordiamoci di dover inserire l’include del file contenente la classe all’inizio.

Possiamo usare l’ereditarietà multipla semplicemente inserendo diverse classi da cui
derivare dopo i : tutte divise da , e ognuna di essa con un proprio indicatore di visibilità

class C: public A, public B{ … }

ADATTATORI
Possiamo definire gli adattatori come delle funzioni che conoscono la classe che  
vogliamo utilizzare e ci permettono di chiamare le sue funzioni in modo più semplice.  
A volte ci permettono anche di migliorare le funzioni offerte o offrono funzioni che sono   “l’unione” di alcune funzioni della classe che vogliamo usare

***Possiamo quindi vederli come una sorta di driver***

## Questioni tecniche polimorfismo dinamico

### 1) ci sono metodi che NON possono essere dichiarati virtuali?
Analizziamo le seguenti funzioni :

● ***Costruttori***: **NO**  
	○ non possono esserlo perché stiamo creando l’oggetto base, esso va  obbligatoriamente creato per creare anche l’oggetto derivato 
	 
● **Distruttori**: **SI ( *obbligatoriamente*)**  
	○ Come avevamo visto tempo fa deve essere virtuale perché se utilizziamo la classe concreta attraverso puntatori alla classe astratta se il distruttore non fosse virtuale non andremmo a distruggere eventuali pezzi di memoria allocati dalla classe derivata  
	
● ***Funzioni membro*** (di istanza, non statiche): **SI** (non obbligatorio)  
	○ si, si intendono le classiche funzioni che abbiamo analizzato fino ad ora  
	
● ***Funzioni membro statiche***: **NO**  
	○ si differenziano dalle normali per il fatto che non fanno capo alle singole istanze ma alla classe direttamente, quindi non si usano attraverso l’operatore this dell’oggetto. Questo vuol dire che non possono essere virtuali per il fatto che quando dovremmo capire il RTTI non possiamo interrogare l’oggetto per capire di  quale classe effettivamente sia 
	 
● ***Template di funzione membro*** (non statiche): **NO**
	○ Si intendono classi in cui è presente al loro interno un template di funzione.  
ES:
Class prova{  
	template <typename T>  
	virtual T funzione(T a, T b){ ... }  
}

Teoricamente potremmo farle virtuali però in realtà viene disabilitato dal  
linguaggio poiché sarebbero difficili da gestire, dato che per ogni istanza  
avremmo che possiamo definire la funzione templatica in vari modi (cioè  
passando ogni volta tipo diversi al template) e questo vorrebbe dire modificare di  
volta in volta la tabella che racchiude i metodi virtuali della classe.  
Ciò da come si può presumere è difficile soprattutto se si vuole essere efficienti

● ***Funzioni membro di un template di classe*** (non statiche): **SI** (non
obbligatorio)
	○ Si intende un template di classe con all’interno delle funzioni
	
ES:
template <typename T>
class animale{
	public:
	virtual void foo(T t){ ... }
}

In questo caso non abbiamo il problema precedente perché una volta
parametrizzato il template di classe (e quindi creando una specifica istanza di
quel template di classe) i metodi virtuali rimangono comunque sempre gli
stessi, non c’è necessita di modificare la tabella dei metodi virtuali, e quindi
si fa in modo analogo a quanto fatto per le classiche funzioni delle classiche
classi

 ##### 2) Come faccio a costruire una copia di un oggetto concreto quando questo mi viene fornito come puntatore/riferimento alla classe base?

//animale.hh
class Animale {
//"costruttori virtuali"
virtual Animale* clone () const=0;
virtual Animale* clone_strana (Cane* pc) const=0;
};

//cane.hh
class Cane : public Animale {
	//"costruttori virtuali"
	Cane* clone() const ovveride {
		return new cane (*this);
	}
};

//utente.cc

void foo (const Cane* pc) {
	//crea una copia
	Animale* pa2 = pc->clone();
	Cane* pc2 = pc->clone();
	
}








Copia di un oggetto concreto tramite puntatore/riferimento a classe base:

#include <iostream>
using namespace std;

class Base { 
    public: 
        int x; 
}; 

int main() 
{ 
    Base b1; 
    b1.x = 10; //assegno a b1 il valore 10 
    Base *b2 = &b1; //creo un puntatore b2 che punta a b1 
    Base b3; //creo un oggetto b3 
  
    b3 = *b2; //effettuo una copia dell'oggetto b1 nell'oggetto b3, tramite il puntatore b2
  
    cout << b3.x; //stampo il valore di x per b3 
  
    return 0; 
} 

//Output: 10

In questo codice viene creata una classe di base con un membro int x. Un oggetto di classe Base viene quindi creato e assegnato al valore 10. Un puntatore b2 viene quindi creato che fa riferimento all'oggetto b1. Un'altro oggetto b3 viene quindi creato ed al suo interno viene effettuata una copia dell'oggetto b1 tramite il puntatore b2. Infine viene stampato il valore di x per b3, che sarà 10.