Open/Closed Principle

Un esempio di violazione del principio OCP (Open-Closed Principle, o principio di apertura-chiusura) in C++ potrebbe essere una classe che rappresenta una forma geometrica, come un quadrato o un cerchio. Se la classe è progettata in modo da poter essere estesa per supportare nuove forme geometriche, ad esempio un triangolo, ma non è possibile modificarla senza rompere la compatibilità con le versioni precedenti, allora viola il principio OCP. 

VIOLAZIONI:

1. Codice duplicato: utilizzare lo stesso codice in più punti della stessa applicazione.

2. Estrema coesione: quando gli elementi di una classe sono strettamente correlati, rendendo difficile la modifica senza influire sugli altri elementi.

3. Estrema dipendenza: quando una classe dipende da un'altra classe per funzionare.

4. Estrema rigidità: quando le classi sono difficili da modificare e richiedono l'intervento diretto del codice.

5. Estrema complessità: quando le classi sono troppo complesse e diventano difficili da comprendere.

6. Estrema fragilità: quando i cambiamenti effettuati in una classe hanno un impatto negativo sull'altra.

7. Estrema instabilità: quando le classi sono soggette a cambiamenti frequenti, rendendo difficile manutenere la stabilità dell'applicazione.





Ecco un esempio di codice che viola questo principio:

Esempio di violazione di OCP:
```
class Student { 
public: 
    void ComputeGrades() { 
        // Calcola i voti dello studente 
    } 
    void PrintGrades() { 
        // Stampa i voti dello studente 
    } 
    void UpdateGrades() { 
        // Aggiorna i voti dello studente 
    } 
}; 
```
ho violato il principio OCP aggiungendo una funzione di aggiornamento alla classe Student che modifica le funzionalità esistenti.


```
// Codice che rispetta OCP
class Student { 
public: 
    virtual void ComputeGrades() = 0; 
}; 

class AdvancedStudent : public Student { 
public: 
    void ComputeGrades() { 
        // Calcola i voti dello studente 
    } 
    void UpdateGrades() { 
        // Aggiorna i voti dello studente 
    } 
}; 
```

ho rispettato il principio OCP facendo in modo che la classe Student sia una classe astratta e la classe AdvancedStudent estenda la classe Student. Questo permette di estendere la classe Student senza dover modificare la classe esistente.


-----------------------------------------------------------------
```
class Automobile {
public:
    int velocita;
    int potenza;

    Automobile(int velocita, int potenza) {
        this->velocita = velocita;
        this->potenza = potenza;
    }

    void incrementaVelocita(int incremento) {
        int limite = 100;
        if (velocita + incremento > limite) {
            velocita = limite;
        } else {
            velocita += incremento;
        }
    }
};
```
Questo codice viola il principio Open/Closed poiché, se si volesse cambiare il limite di velocità, si dovrebbe modificare direttamente il codice della classe Automobile. Piuttosto che modificare direttamente il codice, dovremmo estendere la classe Automobile e sovrascrivere il metodo incrementaVelocita() per specificare un nuovo limite.


---------------------------------------------------------
```
class Automobile {
public:
    int velocita;
    int potenza;

    Automobile(int velocita, int potenza) {
        this->velocita = velocita;
        this->potenza = potenza;
    }

    int getVelocita() {
        return velocita;
    }

    void incrementaVelocita(int incremento) {
        velocita += incremento;
    }
};
```
Questo codice viola anche il principio Open/Closed poiché non è possibile modificare il comportamento di incremento della velocità senza modificare direttamente il codice della classe Automobile. Piuttosto che modificare direttamente il codice, dovremmo estendere la classe Automobile e sovrascrivere il metodo incrementaVelocita() per definire un nuovo comportamento.





---------------------------------------------------------------------
Ecco un esempio di codice che viola il principio OCP in C++:

class Shape {
public:
virtual void draw() = 0;
};

class Circle : public Shape {
public:
void draw() {
// Disegna un cerchio
}
};

class Rectangle : public Shape {
public:
void draw() {
// Disegna un rettangolo
}
};

// A questo punto, viene richiesto di aggiungere la possibilità di disegnare un triangolo

class Shape {
public:
virtual void draw() = 0;

// Aggiungo un nuovo metodo per disegnare un triangolo
void drawTriangle() {
    // Disegno un triangolo
}
};

// Modifico i metodi di disegno del cerchio e del rettangolo per utilizzare il nuovo metodo

class Circle : public Shape {
public:
void draw() {
drawTriangle();
// Disegna un cerchio
}
};

class Rectangle : public Shape {
public:
void draw() {
drawTriangle();
// Disegna un rettangolo
}
};

In questo codice, il principio OCP viene violato poiché la classe Shape viene modificata per aggiungere il metodo di disegno del triangolo, anziché estendere il suo comportamento attraverso una nuova sottoclasse. Inoltre, anche le classi Circle e Rectangle vengono modificate per utilizzare il nuovo metodo, invece di estendere la loro funzionalità attraverso una nuova sottoclasse.

**Ecco un esempio di come correggere il codice per rispettare il principio OCP:**

class Shape {
public:
virtual void draw() = 0;
};

class Circle : public Shape {
public:
void draw() {
// Disegna un cerchio
}
};

class Rectangle : public Shape {
public:
void draw() {
// Disegna un rettangolo
}
};

// A questo punto, viene richiesto di aggiungere la possibilità di disegnare un triangolo

// Creo una nuova sottoclasse di Shape per disegnare un triangolo
class Triangle : public Shape {
public:
void draw() {
// Disegno un triangolo
}
};

// Non modifico le classi esistenti, ma aggiungo una nuova classe per gestire il nuovo requisito

In questo modo, il principio OCP viene rispettato poiché non viene modificato il codice sorgente delle classi esistenti, ma viene creata una nuova classe per gestire il nuovo requisito di disegnare un triangolo.

==***In generale, se il codice viene modificato in modo significativo per aggiungere nuove funzionalità, potrebbe esserci una violazione del principio OCP. La soluzione ideale sarebbe quella di estendere la classe esistente in modo da aggiungere nuove funzionalità senza modificare il codice esistente.***==



**OCP**