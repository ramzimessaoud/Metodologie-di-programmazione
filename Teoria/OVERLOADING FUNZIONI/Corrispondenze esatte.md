Corrispondenze esatte (anche se non lo sono esattamente).  
**- conversioni Lvalue -> Rvalue**  
**- conversioni array -> puntatore**  
**- conversioni funzione -> puntatore**  

Esempi:  
void f(const char*); //1  
void f(char*); //2  
void f(bool); //3  
const char* cp;  

f(cp); //corrispondenza ad 1  
f(0); //ambigua  
f(“ciao”); //corrispondenza ad 2  
f(true); //corrispondenza ad 3  
f(static_cast<char*>(cp)); //corrispondenza ad 2  

**Lvalue**: rappresenta un oggetto che può essere indirizzato.  
**Rvalue**: è un valore che non può essere indirizzato (il valore di un'espressione / una variabile  
temporanea).  

La funzione si aspetta Rvalue come argomenti per i parametri passati per valore (se gli argomenti  
sono nomi di variabili (Lvalue) devono essere convertiti a Rvalue).  
La funzione si aspetta Lvalue come argomenti per i parametri passati per riferimento.  

Esempi:  
#include<string>  
string color("rosso");  
void print(string);  
int main() {  
int a,b,c;  
c=a+b; //l'operatore + richiede una conversione da Lvalue in Rvalue  
print(color); //corrispondenza esatta: conversione, da Lvalue in Rvalue  
return 0;  
}  


###### **Conversioni di qualificazione**  

Riguarda solo i puntatori; si tratta di una conversione che aggiunge il qualificatore const oppure  
volatile al tipo puntato.  
- **Const**: una variabile nasce con un valore che non può essere modifcato (se lo si modifica si va in   undefined beavoir).  
- 
- Volatile: dice che una variabile si può modificare senza che il programma lo sappia.  
Questo serve per registi hardware (sensore che legge la temperatura cambia continuamente il dato  
passato) oppure multytrading (più tread che scrivono nella stessa memoria; ogni tread non sa quale  modifica ha fatto un altro).  

Esempio:  
int *a, *b;  
bool is_equal(const int *, int *);  
int main() {  
if(is_equal(a, b)) cout << "true";  
else cout << "false";  
} //stampa true perché effettua una conversione di qualificazione