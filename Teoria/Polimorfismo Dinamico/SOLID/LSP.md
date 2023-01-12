Liskov Substitution Principle

Liskov Substitution Principle (LSP): Questo principio afferma che gli oggetti nella gerarchia di classi devono essere sostituibili con i loro sottotipi senza cambiamenti nella correttezza del programma. Violazione: Una classe che estende la classe base ma non estende i suoi comportamenti.

1. Violazione della sostituzione della modifica della sottoclasse: una sottoclasse può modificare il comportamento di una superclasse in modo tale che non è più possibile sostituire un'istanza della superclasse con un'istanza della sottoclasse senza cambiare il comportamento dell'applicazione. Ciò significa che se si usa una sottoclasse al posto di una superclasse, l'applicazione potrebbe non funzionare come previsto.

2. Violazione della contrattualizzazione della sottoclasse: una sottoclasse può modificare la firma dei metodi della superclasse in modo tale da non soddisfare più i requisiti contrattuali definiti nella superclasse. Ciò significa che se una sottoclasse cambia la firma di un metodo definito nella superclasse, non sarà più possibile usare il metodo come previsto nell'interfaccia della superclasse.

3. Violazione della struttura degli oggetti: una sottoclasse può modificare la struttura dell'oggetto in modo tale che non è più possibile trattare tutti gli oggetti della stessa classe come se fossero intercambiabili. Ciò significa che le istanze di una sottoclasse potrebbero avere una struttura diversa da quella definita nella superclasse, il che rende difficile sostituire un'istanza della superclasse con un'istanza della sottoclasse.

4. Violazione dell'interfaccia della sottoclasse: una sottoclasse può modificare l'interfaccia della superclasse in modo tale da non rispettare più i criteri di progettazione della stessa. Ciò significa che se una sottoclasse introduce nuove funzionalità che non sono previste nella superclasse, l'applicazione potrebbe non funzionare come previsto.

5. Violazione dell'ereditarietà: una sottoclasse può modificare il comportamento ereditato in modo tale da non soddisfare più le proprietà della superclasse. Ciò significa che se una sottoclasse modifica il comportamento ereditato, l'applicazione potrebbe non funzionare come previsto.

Esempi di violazione:
1. Violazione della sostituzione della modifica della sottoclasse: 

class Superclasse {
public:
    virtual void doSomething() {
        // Codice
    }
};

class Sottoclasse : public Superclasse {
public:
    void doSomething() {
        // Codice che modifica il comportamento della superclasse
    }
};

int main() {
    Superclasse* sclasse = new Sottoclasse();
    sclasse->doSomething(); // Non funzionerà come previsto!
    return 0;
}

2. Violazione della contrattualizzazione della sottoclasse: 

class Superclasse {
public:
    virtual void doSomething(int arg) = 0;
};

class Sottoclasse : public Superclasse {
public:
    void doSomething(float arg) { // La firma del metodo non corrisponde più a quella della superclasse!
        // Codice
    }
};

3. Violazione della struttura degli oggetti: 

class Superclasse {
public:
    int x;
    int y;
};

class Sottoclasse : public Superclasse {
public:
    int z; // La sottoclasse ha una struttura diversa rispetto alla superclasse!
};

4. Violazione dell'interfaccia della sottoclasse:

class Superclasse {
public:
    virtual void doSomething() = 0;
};

class Sottoclasse : public Superclasse {
public:
    void doSomething() {
        // Codice
    } 
    void doSomethingElse() { // La sottoclasse ha introdotto una funzionalità non prevista nella superclasse!
        // Codice
    }
};

5. Violazione dell'ereditarietà: 

class Superclasse {
public:
    virtual void doSomething() {
        // Codice
    }
};


class Sottoclasse : public Superclasse {
public:
    void doSomething() {
        // Codice che modifica il comportamento ereditato
    }
};






Esempio di violazione di LSP:
```
class Rectangle { 
protected: 
    int width; 
    int height; 
public: 
    Rectangle(int w, int h) { 
        width = w; 
        height = h; 
    } 
    int GetWidth() { 
        return width; 
    } 
    int GetHeight() { 
        return height; 
    } 
    int Area() { 
        return width * height; 
    } 
}; 

class Square : public Rectangle { 
public: 
    Square(int s) : Rectangle(s, s) { 
    } 
    int GetWidth() { 
        return Rectangle::GetWidth(); 
    } 
    int GetHeight() { 
        return Rectangle::GetHeight(); 
    } 
};
```

ho violato il principio LSP inserendo una classe Square che estende la classe Rectangle ma non estende i suoi comportamenti.



```
// Codice che rispetta LSP
class Rectangle { 
protected: 
    int width; 
    int height; 
public: 
    Rectangle(int w, int h) { 
        width = w; 
        height = h; 
    } 
    int GetWidth() { 
        return width; 
    } 
    int GetHeight() { 
        return height; 
    } 
    virtual int Area() { 
        return width * height; 
    } 
}; 

class Square : public Rectangle { 
public: 
    Square(int s) : Rectangle(s, s) { 
    } 
    int GetWidth() { 
        return Rectangle::GetWidth(); 
    } 
    int GetHeight() { 
        return Rectangle::GetHeight(); 
    } 
    int Area() { 
        return Rectangle::Area(); 
    } 
}; 
```
ho rispettato il principio LSP fornendo una classe Square che estende la classe Rectangle. Tuttavia, ho anche assicurato che la classe Square estenda i comportamenti della classe Rectangle.



1. Una violazione del principio LSP si presenta quando un sottotipo non può essere sostituito al supertipo senza compromettere il comportamento del programma.

2. Una violazione del principio LSP si può riconoscere quando un sottotipo fornisce un comportamento diverso dal supertipo, che può renderlo incompatibile con il contesto in cui deve operare.

3. Una violazione del principio LSP può essere identificata quando un sottotipo non rispetta le proprietà definite nel supertipo.

4. Una violazione del principio LSP si può riconoscere quando un sottotipo introduce nuove proprietà che non sono state definite nel supertipo.

5. Una violazione del principio LSP si può riconoscere quando un sottotipo non implementa tutte le operazioni definite nel supertipo.

6. Una violazione del principio LSP si può riconoscere quando un sottotipo introduce operazioni non previste dal supertipo, che potrebbero rendere incompatibile il sottotipo con il contesto in cui deve operare.

3 esempi di violazione :

1. Un esempio di violazione del principio LSP in C++ è quando si crea una classe derivata che fornisce un comportamento diverso da quello del suo supertipo. Ad esempio, se una classe Base fornisce un'implementazione del metodo draw (), ma la classe Derived non implementa il metodo draw (), ciò costituisce una violazione del principio LSP.

2. Un altro esempio di violazione del principio LSP in C++ è quando una classe derivata introduce nuove proprietà che non sono state definite nel supertipo. Ad esempio, se una classe Base definisce un membro dati int, ma la classe Derived introduce un membro dati double, ciò costituisce una violazione del principio LSP.

3. Un ulteriore esempio di violazione del principio LSP in C++ è quando una classe derivata non implementa tutte le operazioni definite nel supertipo. Ad esempio, se una classe Base definisce un metodo foo (), ma la classe Derived non definisce un metodo foo (), ciò costituisce una violazione del principio LSP.

````
class Base {
public:
    virtual void draw() = 0;
};

class Derived : public Base {
public:
    // La classe Derived non implementa il metodo draw(), violando così il principio LSP
};

int main() {
    Base* b = new Derived(); // Creazione di un oggetto di tipo Derived
    b->draw(); // Violazione del principio LSP, poiché la classe Derived non implementa il metodo draw()
    return 0;
}
``

**LSP**

SRP