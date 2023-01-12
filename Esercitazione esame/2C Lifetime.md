OVERRIDING (LEZIONE 19 2022)

passo 1:
creazione variabili globali (sia prima che dopo il main)
creazione oggetti (derivate creano base e sucessivamente se stessa) 

N.B. Le throw all'interno dei costruttori, interompono l'esecuzione e si entra in modalità eccezionale e si distrugge tutte le costruzioni costruite all'interno di quella chiamata.

passo 2: 
overloading funzioni (match perfetto - promozione - conversione std)

passo 3 
overriding (solo match perfetto funzioni + scritta virtual in classe base)
	solo se chiamati tramite puntatore ->

passo 4
distruzione (in ordine inverso alla creazione)


ESERCITAZIONE LIFETIME ELLY 2019/2020
````
/*
  Lo scopo di questo esercizio è quello di verificare la conoscenza
  di alcune nozioni la cui comprensione è essenziale per affrontare
  (in maniera consapevole) le tematiche relative alla corretta gestione
  delle risorse e, in particolare, della exceptin safety.

  Le nozioni in questione sono:
    - tempo di vita degli oggetti
    - costruzione e distruzione di oggetti
    - composizione di tipi di dato (aggregazione e ereditarietà)
    - flussi di esecuzione eccezionali

  L'esercizio consiste nel *prevedere* l'output prodotto dal programma
  (indicando con precisione la sequenza corretta dei vari messaggi).
  Sebbene sia possibile (e facile) compilare ed eseguire il codice per
  *vedere* l'output, il consiglio è quello di farlo soltanto in un secondo
  momento, come utile feedback per verificare se le proprie previsioni
  erano accurate e, nel caso, chiedersi come mai non lo erano.
  Tipicamente, si scoprirà di essere incappati in qualche banale svista,
  ma in alcuni casi occorrerà ammettere che certi meccanismi di base
  non erano stati compresi a fondo.
*/

#include <iostream>

// ---------------------------

struct C1 {
  int i1;
  C1() {
    std::cerr << "Constructor C1::C1()" << std::endl;
  }
  ~C1() {
    std::cerr << "Destructor C1::~C1()" << std::endl;
  }
};

// ---------------------------

struct C2 {
  int i2;
  C2() {
    std::cerr << "Constructor C2::C2()" << std::endl;
    throw 123;
  }
  ~C2() {
    std::cerr << "Destructor C2::~C2()" << std::endl;
  }
};

// ---------------------------

struct C3 : public C1 {
  int i3;
  C3() {
    std::cerr << "Constructor C3::C3()" << std::endl;
  }
  ~C3() {
    std::cerr << "Destructor C3::~C3()" << std::endl;
  }
};

// ---------------------------

// D:{ C1:{ i1 }, i4, c3:C3{ C1:{ i1 }, i3 }, c2:C2{ i2 }, c1:C1{ i1 } }

class D : public C1 {
private:
  int i4;
  C3 c3;
  C2 c2;
  C1 c1;

public:
  D() : c1(), c2(), c3() {
    std::cerr << "Constructor D::D()" << std::endl;
  }
  ~D() {
    std::cerr << "Destructor D::~D()" << std::endl;
  }
};

// ---------------------------

C3 c3;

// ---------------------------

int main() {
  std::cout << "Start" << std::endl;
  try {
    C1 c1;
    D d;
  }
  catch (char c) {
    std::cerr << "char " << c << std::endl;
  }
  catch (...) {
    std::cerr << "..." << std::endl;
  }
  std::cout << "End" << std::endl;
  return 0;
}

// ---------------------------

C1 c1;

// ---------------------------
````

output:
//C3 c3 
Constructor C1::C1()
Constructor C3::C3()
//C1 c1
Constructor C1::C1()
//main
Start
//C1 c1
Constructor C1::C1()
//D d
Constructor C1::C1()
Constructor C1::C1()
Constructor C3::C3()
Constructor C2::C2() // fallita
Destructor C3::~C3()
Destructor C1::~C1()
Destructor C1::~C1()
//thorw 123  
...
End
Destructor C1::~C1()

Destructor C1::~C1()
Destructor C3::~C3()







1 febbraio 2005

Indicare l’output prodotto dal seguente programma.
````
class Base {
public:
Base() {
std::cout << "Constructor Base::Base()" << std::endl;
}
virtual void f(int) {
std::cout << "Base::f(int)" << std::endl;
}
virtual void f(double) {
std::cout << "Base::f(double)" << std::endl;
}
virtual void g(int) {
std::cout << "Base::g(int)" << std::endl;
}
virtual ~Base() {
std::cout << "Destructor Base::~Base()" << std::endl;
}
};
class Derived : public Base {
public:
Derived() {
std::cout << "Constructor Derived::Derived()" << std::endl;
}
void f(char c) {
std::cout << "Derived::f(char)" << std::endl;
}
void g(int) {
std::cout << "Derived::g(int)" << std::endl;
}
~Derived() {
std::cout << "Destructor Derived::~Derived()" << std::endl;
}
};
int main() {
Base b; //creo oggetto di tipo base
Derived d; //creo oggetto di tipo base e un oggetto di tipo derived
Base& rb = b; //riferimento a base
Base* pb = &d; //puntatore a base (d) statico puntatore a derived dinamico (mismatch)
std::cout << "=== 1 ===" << std::endl;
b.f(1);
rb.f('a');
rb.g(1);
std::cout << "=== 2 ===" << std::endl;
d.f(1);
d.f(1.0);
d.g(3.3);
std::cout << "=== 3 ===" << std::endl;
pb->f(1.0);
pb->f('a');
pb->g(3.3);
return 0;
}
````

output:
````
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
Base::f(double) //il compilatore trova virtual ma nessuna classe riscrive la funzione
Base::f(int)  //il compilatore trova virtual ma nessuna classe riscrive la funzione
Derived::g(int) // il compilatore trova virtual e trova la classe g(int) riscritta in derived
Destructor Derived::~Derived()
Destructor Base::~Base()
Destructor Base::~Base()
````

