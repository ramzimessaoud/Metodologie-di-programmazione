Guida sulla correzione di un codice dato 

 - Controlliamo se le classi/interfacce rispettano i Principi **SOLID**
	- **[[LSP]]**: Ogni interfaccia deve avere un solo compito
		-  risolviamo dividendo i vari compiti in diverse interfacce
		
	- [[OCP]]:  una classe dovrebbe essere aperta per l'estensione ma chiusa per la modifica.
		- N.B. se non modificalbile uso classi adattatori.
	
	- [[LSP]]:  le classi derivate dovrebbero essere sostituibili con le loro classi base.
		- Una volta identificata , è necessario identificare la causa e, se necessario, modificare la sottoclasse in modo che soddisfi i requisiti della superclasse, garantendo che la struttura dell'oggetto e le proprietà ereditate non vengano modificate e che non vengano aggiunte funzionalità non previste.
		
	- [[ISP]]: i client non devono essere forzati a dipendere da interfacce che non usano. Violazione: Un'interfaccia che fornisce troppe funzionalità che non sono necessarie per un client.
		- risolviamo dividendo l'interfaccia in piccole sottoclassi rimuovendo i metodi che non servono.
		
	- [[DIP]]: le dipendenze tra le classi dovrebbero essere basate sull'interfaccia, non sull'implementazione.
		- risolviamo rimuovendo tutte le cattive dipendenze, facendo si che tutte le classi dipendano da una o più interfaccie astratte (ereditarietà multipla)
		- N.B. se non modificalbile uso classi adattatori.
		
 - Controlliamo che le interfacce sono virtual e sono pure, hanno un distruttore non puro

 - Rispetti il principio "I[[IS-A]]"
