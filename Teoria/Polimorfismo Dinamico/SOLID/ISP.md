 Interface Segregation Principle
 
Interface Segregation Principle (ISP): Questo principio afferma che i client non devono essere forzati a dipendere da interfacce che non usano. Violazione: Un'interfaccia che fornisce troppe funzionalità che non sono necessarie per un client.

Esempio di violazione di ISP:
```
class UserInterface { 
public: 
    void DisplayText() { 
        // Visualizza il testo 
    } 
    void DisplayImages() { 
        // Visualizza le immagini 
    } 
    void PlayAudio() { 
        // Riproduce l'audio 
    } 
}; 
```
ho violato il principio ISP fornendo un'interfaccia che fornisce troppe funzionalità che non sono necessarie per un client.


```
// Codice che rispetta ISP
class TextDisplay { 
public: 
    void DisplayText() { 
        // Visualizza il testo 
    } 
}; 

class ImageDisplay { 
public: 
    void DisplayImages() { 
        // Visualizza le immagini 
    } 
}; 

class AudioPlayer { 
public: 
    void PlayAudio() { 
        // Riproduce l'audio 
    } 
}; 
```
ho rispettato il principio ISP dividendo l'interfaccia UserInterface in tre interfacce separate: TextDisplay, ImageDisplay e AudioPlayer. Ciò significa che i clienti saranno in grado di utilizzare solo le interfacce che hanno bisogno.

**ISP**