L'ereditarietà multipla è un concetto in programmazione orientata agli oggetti che si riferisce alla capacità di una classe di estendere più di una classe padre. In altre parole, significa che una classe può ereditare i metodi e le proprietà di più classi padre, invece di solo una.

Per spiegare meglio, immaginiamo di avere tre classi: A, B e C. La classe A può essere la classe padre di B e C, mentre B e C sono le classi figlie di A. In questo caso, possiamo dire che B e C ereditano i metodi e le proprietà della classe A. Tuttavia, se vogliamo che B erediti anche da C, possiamo farlo con l'ereditarietà multipla. In questo caso, B sarebbe una classe figlia di A e di C, e quindi erediterebbe metodi e proprietà da entrambe le classi.

L'ereditarietà multipla può essere utile in diversi casi, ad esempio quando si vuole riutilizzare il codice di una classe in più classi figlie, o quando si vuole creare una classe figlia che combini le funzionalità di più classi padre. Tuttavia, l'ereditarietà multipla può anche rendere il codice più complesso e difficile da gestire, quindi è importante valutare attentamente se è davvero necessaria in un determinato caso.

```
// Classe A - classe padre
class A
{
public:
    void methodA() { /* codice per il metodo A */ }
    int propertyA;
};

// Classe B - classe figlia di A
class B : public A
{
public:
    void methodB() { /* codice per il metodo B */ }
    int propertyB;
};

// Classe C - classe figlia di A
class C : public A
{
public:
    void methodC() { /* codice per il metodo C */ }
    int propertyC;
};

// Classe D - classe figlia di B e C
class D : public B, public C
{
public:
    // In questo caso, D eredita i metodi e le proprietà
    // sia da B che da C, quindi può chiamare methodA,
    // methodB, methodC e usare propertyA, propertyB, propertyC
    void methodD()
    {
        methodA();
        methodB();
        methodC();
    }

    int propertyD;
};
```


EREDITARIETA MULTIPLA