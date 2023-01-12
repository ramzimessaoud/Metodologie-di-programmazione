Le guardie contro la definizione doppia (anche conosciute come "guardie di inclusione") sono utilizzate in C++ per evitare la definizione doppia di entità all'interno di un file di intestazione. Queste guardie sono costituite da un blocco di codice che viene eseguito solo se una determinata condizione è vera.

Le guardie contro la definizione doppia sono generalmente utilizzate per proteggere le intestazioni dall'essere incluse più volte in un file sorgente durante la compilazione. Ad esempio, se un file di intestazione contiene la definizione di una classe, le guardie contro la definizione doppia possono essere utilizzate per evitare che la classe venga definita più volte nel file sorgente che include l'intestazione.

Le guardie contro la definizione doppia sono generalmente costituite da una serie di direttive del preprocessore del compilatore C++, come ad esempio #ifndef, #define e #endif. Una volta che una guardia contro la definizione doppia viene definita in un file di intestazione, il compilatore la utilizzerà per verificare se il file è già stato incluso in precedenza nel file sorgente. Se il file non è stato incluso, il codice all'interno della guardia viene eseguito; se invece il file è già stato incluso, il codice all'interno della guardia viene ignorato.

Ecco un esempio di utilizzo di guardie contro la definizione doppia in C++:
````
#ifndef MYCLASS_H
#define MYCLASS_H

// Codice della classe MyClass qui

#endif // MYCLASS_H
````
In questo esempio, la guardia contro la definizione doppia verifica se MYCLASS_H è già stato definito. Se non lo è, il codice della classe MyClass viene incluso nel file sorgente. Se invece MYCLASS_H è già stato definito, il codice della classe MyClass viene ignorato. Questo aiuta a evitare la definizione doppia della classe MyClass nel file sorgente.

NOTA: questa soluzione, per funzionare, deve essere applicata
sistematicamente su *tutti* gli header file che fanno parte del
programma (Polinomio.hh, eccetera); inoltre, occorre prestare
attenzione e evitare di usare la stessa guardia (ovvero, flag del
preprocessore con lo stesso nome) per file di inclusione distinti.

NOTA: verificare che le guardie contro l'inclusione ripetuta sono
utilizzate negli header file della libreria standard distribuiti
con g++. Per esempio, nell'header file iostream:
````
#ifndef _GLIBCXX_IOSTREAM
#define _GLIBCXX_IOSTREAM 1

// ...

#endif /* _GLIBCXX_IOSTREAM */
````