1.  Definire la funzione generica "find_all" che prende in input una sequenza e un elemento e restituisce in output una sequenza di iteratori che puntano a tutte le occorrenze dell'elemento nella sequenza. La funzione deve essere implementata senza fare ricorso ad altri algoritmi generici della libreria standard. Ad esempio:
````
template <typename Iter, typename T> 
std::vector<Iter> find_all(Iter first, Iter last, const T& value) { 
	std::vector<iter> result;
	for( ; first!=last; ++first)
	{
		if(*first== value)
		result.push_back(*first);	
	}
	return result;
}
````

2.  Definire la funzione generica "union_sets" che prende in input due sequenze di elementi distinti e restituisce in output una sequenza di elementi che rappresenta l'unione di queste sequenze. La funzione deve essere implementata senza fare ricorso ad altri algoritmi generici della libreria standard. Ad esempio:

````
template <typename Iter1, typename Iter2, typename OutputIter> 
void union_sets(Iter1 first1, Iter1 last1, Iter2 first2, Iter2 last2, OutputIter result) { // ... }
````

3.  Definire la funzione generica "intersect_sets" che prende in input due sequenze di elementi e restituisce in output una sequenza di elementi che rappresenta l'intersezione di queste sequenze. La funzione deve essere implementata senza fare ricorso ad altri algoritmi generici della libreria standard. Ad esempio:
````
template <typename Iter1, typename Iter2, typename OutputIter> 
void intersect_sets(Iter1 first1, Iter1 last1, Iter2 first2, Iter2 last2, OutputIter result) { // ... }
````
4. Definire la funzione generica contains che prende in input due sequenze (non necessariamente dello stesso tipo) e restituisce in output un booleano. La funzione restituisce il valore true se e solo se ogni elemento della seconda sequenza compare, in una posizione qualunque, nella prima sequenza.  Nota: si richiede di implementare la funzione senza fare ricorso ad altri algoritmi generici della  libreria standard.

5. Definire la funzione generica "is_palindrome" che prende in input una sequenza di elementi e restituisce in output un valore booleano che indica se la sequenza è palindroma (ovvero se è uguale a se stessa quando viene letta al contrario). La funzione deve essere implementata senza fare ricorso ad altri algoritmi generici della libreria standard. Ad esempio:
````
template <typename Iter> bool 
is_palindrome(Iter first, Iter last) { // ... }
````

6.  Definire la funzione generica "reverse_copy" che prende in input una sequenza di elementi e un iteratore di output, e copia gli elementi della sequenza nell'iteratore di output in ordine inverso. La funzione deve essere implementata senza fare ricorso ad altri algoritmi generici della libreria standard. Ad esempio:

````
template <typename Iter1, typename ForwardIter2> ForwardIter2 reverse_copy(Iter1 first, Iter1 last, ForwardIter2 result) { // ... }
````

7.  Definire la funzione generica "sort" che prende in input una sequenza di elementi e li ordina in modo crescente. La funzione deve essere implementata senza fare ricorso ad altri algoritmi generici della libreria standard. Ad esempio:

```template <typename Iter> void sort(Iter first, Iter last) { // ... }``