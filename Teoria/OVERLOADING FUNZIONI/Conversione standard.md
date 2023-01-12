Se il tipo dell'argomento non corrisponde al tipo del parametro, né esattamente né per promozione, allora si applicano le regole di conversone standard:

- Conversioni intere: ogni argomento di tipo integrale o enumerazione è convertito in un altro tipo
integrale.
- Conversioni in virgola mobile: ogni argomento di tipo floating è convertito in un altro tipo
floating.
- Conversione virgola mobile-intero: ogni tipo integrale è convertito ad un tipo floating e viceversa.
- Conversione di puntatori: 0 è convertito in un tipo puntatore; ogni tipo puntatore è convertito a
void.
- Conversioni booleane: Ogni argomento di tipo intero, tipo in virgola mobile, enumeratore o
puntatore è convertito al tipo bool.
Esempi:
void f(unsigned int); //1
void f(float); //2
void f(void*); //3
char *cp;

f(‘a’); //ambigua, corrisponde a 1 e 2
f(0); //ambigua, corrisponde a 1, 2, e 3
f(3.14); //ambigua, corrisponde a 1 e 2
f(cp); //corrisponde a 3