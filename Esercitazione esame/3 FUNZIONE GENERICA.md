Fuznione generica, algoritmo generico, iteratori e inseritori

# **Algoritmi che accedono in lettura:

-find 	-> trova l'elemento nella sequenza, restituisce l'iteratore

````
template <typename Iter, typename T>
Iter find(Iter first, Iter last, const T& value) {
    for (Iter it = first; it != last; ++it) {
        if (*it == value) {
            return it;
        }
    }
    return last;
}
````

-count	-> conta le istanze uguali al valore passato

````
template <typename InputIterator, typename T> 
unsigned count (InputIterator first, InputIterator last, const T& val){
  unsigned res = 0;
  while (first!=la  st) {
    if (*first == val) ++ret;
    ++first;
  }
  return res;
}
````

-equal -> confronta due sequenze
````
Versione 1:
template <typename Iter1, typename Iter2> 
bool
equal(Iter1 first1, Iter1 last1, Iter2 first2, Iter2 last2){
  for( ; first1 != last1 && first2 != last2; ++first1, ++first2){
  if(*first1!=*first2)
	return false;
  }
 return (first1 == last1 && first2 == last2) //Sono entrambe finite 
}
````
Versione 2: 
Per quando l'utente è sicuro che la seconda sequenza sia lunga almeno quanto la prima
````
template <typename Iter1, typename Iter2> 
bool
equal(Iter1 first1, Iter1 last1, Iter2 first2){
  for( ; first1 != last1; ++first1, ++first2){
  if(*first1!=*first2)
	return false;
  }
 return true;
}
````

````

template <typename Iter, typename UnaryPred>  //Unary perché un argomento 
Iter 
find_if(Iter first, Iter last, UnaryPred pred){
  for( ; first!=last; ++first){
	if(pred(*first))
	  return first
  }
return last; //Non ho trovato		  
}
````
 -count_if	-> conta se soddisfa la data condizione
````
template <typename Iter, typename Pred>
int count_if(Iter first, Iter last, Pred p) {
    int count = 0;
    for (Iter it = first; it != last; ++it) {
        if (p(*it)) {
            ++count;
        }
    }
    return count;
}
````

-all_of, any_of, none_of -> bool che ritorna vero se tutti/qualcuno/nessuno degli elementi soddisfa la condizione
````
template <typename Iter, typename Pred>
bool all_of(Iter first, Iter last, Pred p) {
    for (Iter it = first; it != last; ++it) {
        if (!p(*it)) {
            return false;
        }
    }
    return true;
}
````

````
template <typename Iter, typename Pred>
bool any_of(Iter first, Iter last, Pred p) {
    for (Iter it = first; it != last; ++it) {
        if (p(*it)) {
            return true;
        }
    }
    return false;
}
````

````
template <typename Iter, typename UnaryPred> 
bool none_of(Iter first, Iter last, UnaryPred pred){
	return find_if(first, last, pred) == last;

}
````

-adjacent_find -> trova il primo elemento uguale all'elemento immediatamente successivo (restituisce un iteratore)
````
template <typename Iter>
Iter
adjacent_find (Iter first, Iter last){
  if (first == last) //Sequenza vuota
	return last;
  Iter next = first;
  ++next;
  for ( ; next!= last; ++first, ++next){
  	if(*first == *next)
	  return first;
  } 
return last;	//Non ci sono elementi adiacenti uguali 
}
````

-find_first_of -> trova all'interno di 2 sequenze la prima occorrenzanella prima sequenza, di un elemento qualsiasiche compare in una qualsiasi posizione nella seconda sequenza.

````
template <typename Iter1, typename FwdIter2>
InIter1	//Puntatore all'interno della prima sequenza
find_first_of(InIter1 first1, InIter1 last1, FwdIter2 first2, FwdIter2 last2){
  for(; first1 != last1; ++first1){
   	//controlla se *first1 compare in [first2, last2)
	for(FwdIter2 i2 = first2; i2 != last2; ++i2){
	//creando un copia di first2 non lo andiamo a modificare
	  if(*first1==*i2)
	    return first1;
	}
  }
  return last1; //Non trovato
}
````



# **Algoritmi che modificano sequenze in scrittura:

Gli algoritmi che utilizzano **l'iteratore output** scrivono i loro risultati in una destinazione specificata attraverso l'iteratore di output. Ciò consente di scrivere i risultati in una varietà di strutture di dati, come array, contenitori e stream di output.

Operazioni:
avanzamento ``++Outiter``
accesso in scritutta  ``*iter= valore``


Ecco alcuni esempi di algoritmi che utilizzano l'iteratore output:

- `std::copy`: copia gli elementi di un intervallo di input in un intervallo di output.
````
template<typename InputIt, typename OutputIt>
OutputIt copy(InputIt first, InputIt last, OutputIt d_first)
{
    // Copia gli elementi di [first, last) in d_first
    while (first != last)
    {
        *d_first++ = *first++;
    }
    return d_first;
}
````
-   `std::transform`: trasforma gli elementi di un intervallo di input utilizzando una funzione specificata e li scrive in un intervallo di output.
 ````
 template<typename InputIt, typename OutputIt, typename UnaryOperator>
OutputIt transform(InputIt first, InputIt last, OutputIt d_first, UnaryOperator op)
{
    // Trasforma gli elementi di [first, last) utilizzando op e li scrive in d_first
    while (first != last)
    {
        *d_first++ = op(*first++);
    }
    return d_first;
}
````
-   `std::replace_copy`: copia gli elementi di un intervallo di input in un intervallo di output sostituendo tutte le occorrenze di un elemento specifico con un altro elemento specifico.
````
template<typename InputIt, typename OutputIt, typename T>
OutputIt replace_copy(InputIt first, InputIt last, OutputIt d_first,
                      const T& old_value, const T& new_value)
{
    // Copia gli elementi di [first, last) in d_first sostituendo ogni occorrenza di old_value con new_value
    while (first != last)
    {
        *d_first++ = (*first == old_value) ? new_value : *first;
        ++first;
    }
    return d_first;
}
````
-  `std::reverse_copy`: copia gli elementi di un intervallo di input in un intervallo di output in ordine inverso.
````
template<typename BidirIt, typename OutputIt>
OutputIt reverse_copy(BidirIt first, BidirIt last, OutputIt d_first)
{
    // Copia gli elementi di [first, last) in d_first in ordine inverso
    while (first != last)
    {
        --last;
        *d_first++ = *last;
    }
    return d_first;
}
````
-   `std::remove_copy`: copia gli elementi di un intervallo di input in un intervallo di output rimuovendo tutte le occorrenze di un elemento specifico.
````
template<typename InputIt, typename OutputIt, typename T>
OutputIt remove_copy(InputIt first, InputIt last, OutputIt d_first, const T& value)
{
    // Copia gli elementi di [first, last) in d_first rimuovendo ogni occorrenza di value
    while (first != last)
    {
        if (*first != value)
        {
            *d_first++ = *first;
        }
        ++first;
    }
    return d_first;
}
````


Gli iteratori output e forward sono entrambi tipi di iteratore che vengono utilizzati per l'output di dati da un algoritmo. Tuttavia, ci sono alcune differenze importanti tra loro:

-   L'iteratore output è utilizzato solo per scrivere i dati in una destinazione specificata. Non è possibile utilizzarlo per leggere i dati dalla destinazione.
-   L'iteratore forward è in grado sia di leggere che di scrivere i dati. Inoltre, è in grado di spostarsi verso l'inizio o la fine dell'intervallo dei dati a cui fa riferimento.

Per questo motivo, gli algoritmi che richiedono la possibilità di leggere e scrivere i dati o di spostarsi all'interno dell'intervallo utilizzano spesso l'iteratore forward, mentre gli algoritmi che hanno solo bisogno di scrivere i dati utilizzano spesso l'iteratore output.


# **Algoritmi che modificano sequenze in Sovrascrittura:

Gli algoritmi di sovrascrittura utilizzano **l'iteratore forward** per modificare gli elementi di un intervallo di dati in modo che siano ordinati o che non contengano duplicati. L'iteratore forward è sufficientemente versatile da consentire l'accesso in lettura e scrittura agli elementi di un intervallo e di spostarsi verso l'inizio o la fine dell'intervallo (riavvolgibili).

Ecco alcuni esempi di implementazione di algoritmi di sovrascrittura che utilizzano l'iteratore forward:
````
template<typename ForwardIt>
void sort(ForwardIt first, ForwardIt last)
{
    // Ordina gli elementi di [first, last) in modo crescente
    if (first == last)
        return;

    for (ForwardIt i = first; i != last; ++i)
    {
        for (ForwardIt j = i; j != last; ++j)
        {
            if (*j < *i)
                std::swap(*i, *j);
        }
    }
}
````

````
template<typename ForwardIt>
ForwardIt unique(ForwardIt first, ForwardIt last)
{
    // Rimuove gli elementi duplicati da [first, last)
    if (first == last)
        return last;

    ForwardIt result = first;
    while (++first != last)
    {
        if (*result != *first)
            *++result = *first;
    }
    return ++result;
}
````

````
template<typename ForwardIt>
void reverse(ForwardIt first, ForwardIt last)
{
    // Inverte l'ordine degli elementi di [first, last)
    while ((first != last) && (first != --last))
    {
        std::swap(*first++, *last);
    }
}
````

````
template <typename FwdIter, typename UnaryPred>
FwdIter
remove_if (FwdIter first, FwdIter last, UnaryPred pred){
  first = find_if (first, last, pred);
  if (first == last)
	return first;
  FwdItr res = first;
  ++first;			//Questo impone FwdIter
  for( ; first != last; ++first) 
	if(!pred(first)){	//NON deve essere rimosso
  	  *res = *first;	//Li copio nella posizione che avranno 
	  ++res;
  	}
  return res;			//Punta al primo elemento dei rimossi
}
````

# **Algoritmi che modificano sequenze in Inserimento:
3 tipi:

std::back_insert_iterator inserisce sempre infondo

std::insert_front_iterator inserisce sempre davanti (non funziona con vector)

std::Insert_iterator inserisce nella posizione che li forniamo.
````
template<typename InputIt, typename Container>
void copy(InputIt first, InputIt last, Container& c)
{
    // Copia gli elementi di [first, last) in c utilizzando back_inserter
    std::copy(first, last, std::back_inserter(c));
}
````

````
template<typename InputIt, typename Container, typename UnaryOperator>
void transform(InputIt first, InputIt last, Container& c, UnaryOperator op)
{
    // Trasforma gli elementi di [first, last) utilizzando op e li inserisce in c utilizzando back_inserter
    std::transform(first, last, std::back_inserter(c), op);
}
````

````
template<typename InputIt, typename Container, typename T>
void replace_copy(InputIt first, InputIt last, Container& c,
                  const T& old_value, const T& new_value)
{
    // Copia gli elementi di [first, last) in c sostituendo ogni occorrenza di old_value con new_value utilizzando inserter
    std::replace_copy(first, last, std::inserter(c, c.end()), old_value, new_value);
}
````

````
template<typename InputIt, typename Container, typename T>
void remove_copy(InputIt first, InputIt last, Container& c, const T& value)
{
    // Copia gli elementi di [first, last) in c rimuovendo ogni occorrenza di value utilizzando back_inserter
    std::remove_copy(first, last, std::back_inserter(c), value);
}
````