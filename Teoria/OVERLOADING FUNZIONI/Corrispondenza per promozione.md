Se il tipo dell'argomento non corrisponde esattamente al tipo del parametro, allora lo si promuove:  

###### Da                                                                                           A

boolean, char, unsigned char, short                                     int

unsigned short                                                                     int, unsigned int

float                                                                                      Double



Gli argomenti enumerati (contatori) vengono promossi al più piccolo tipo integrale che può  
contenerli nell’ordine: int, unsigned int, long, unsigned long.  

Esempi:  
enum colore { bianco : 100, nero : 1000, viola : OxFFFFFFFF};  
void f(unsigned int); //1  
void f(int); //2  
void f(char); //3  
f(u’a’); //corrisponde a 2  
f(0); //corrisponde a 2  
f(true); //corrisponde a 2  
f(viola); //corrisponde a 1


Tipo primitivo a tipo primitivo

Da | A
-- | --
int | double
float | long
char | short

Tipo primitivo a riferimento

Da | A
-- | --
int | int*
float | float*
char | char*

Riferimento a riferimento

Da | A
-- | --
int* | void*
float* | void*
char* | void*

Riferimento a tipo primitivo

Da | A
-- | --
int* | int
float* | float
char* | char

Autoboxing

Da | A
-- | --
int | int*
float | float*
char | char*

Unboxing

Da | A
-- | --
int* | int
float* | float
char* | char