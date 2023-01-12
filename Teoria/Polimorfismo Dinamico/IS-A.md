IS-A  è un principio di progettazione orientato agli oggetti che descrive una relazione di ereditarietà tra classi. Ad esempio, se si definisce una classe Animale:

class Animale {
public:
    virtual void muovi() = 0;
};

allora si può definire una sottoclasse Pesce come segue:

class Pesce : public Animale {
public:
    void muovi() {
        // Codice per muovere il pesce
    }
};

In questo caso, la classe Pesce estende la classe Animale, il che significa che la classe Pesce "è un" Animale. Ciò significa che un'istanza della classe Pesce può essere trattata come un'istanza della classe Animale, il che significa che tutte le proprietà e i metodi definiti nella classe Animale sono anche disponibili nella classe Pesce.