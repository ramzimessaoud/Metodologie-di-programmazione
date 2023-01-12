----------------------------------------

** Risoluzione overriding

Viene effettuata a tempo di esecuzione dal RTS (supporto a tempo di
esecuzione). Si noti che, in ogni caso, a tempo di compilazione
viene fatta la risoluzione dell'overloading nel solito modo.
Affinché si attivi l'overriding occorre che:

1) il metodo invocato sia un metodo virtuale
   (esplicitamente o implicitamente, se ereditato da una classe base);

2) il metodo viene invocato tramite puntatore o riferimento
   (altrimenti non vi può essere distinzione tra il tipo statico
   e il tipo dinamico dell'oggetto e quindi si invoca il metodo
   della classe base);

3) almeno una delle classi lungo la catena di derivazione che porta
   dal tipo statico al tipo dinamico ha effettuato l'overriding
   (in assenza di overriding, si invoca il metodo della classe base);

4) il metodo NON deve essere invocato mediante qualificazione esplicita
   (la qualificazione esplicita causa l'invocazione del metodo come
   definito nella classe usata per la qualificazione).

-------------------------------------------