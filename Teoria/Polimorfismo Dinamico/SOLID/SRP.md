Single Responsibility Principle

Un esempio di violazione del principio SRP (Single Responsibility Principle, o principio di unica responsabilità) potrebbe essere una classe di un programma che ha il compito sia di gestire l'input dell'utente sia di elaborare i dati e produrre l'output. In questo caso, la classe avrebbe due responsabilità distinte che dovrebbero essere gestite da due classi separate, in modo da rendere il codice più facile da mantenere e modificare.

```class UserInputProcessor {
public:
    void handleInput()
    {
        // Gestisce l'input dell'utente
    }

    void processData()
    {
        // Elabora i dati
    }

    void produceOutput()
    {
        // Produce l'output
    }
};
```


In questo caso, la classe `UserInputProcessor` ha tre responsabilità distinte: gestire l'input dell'utente, elaborare i dati e produrre l'output. Invece, sarebbe più corretto avere tre classi separate, una per ogni responsabilità:
```
class UserInputHandler
{
public:
    void handleInput()
    {
        // Gestisce l'input dell'utente
    }
};

class DataProcessor
{
public:
    void processData()
    {
        // Elabora i dati
    }
};

class OutputProducer
{
public:
    void produceOutput()
    {
        // Produce l'output
    }
};
```
In questo modo, il codice risulta più organizzato e facile da modificare e mantenere, poiché ogni classe ha una sola responsabilità.