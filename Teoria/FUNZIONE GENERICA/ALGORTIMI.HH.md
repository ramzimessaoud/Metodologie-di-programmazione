Scriveremo dei algortimi generici:

iteratore è un concetto quindi lo definiamo come template typename iter, il valore invece lo definiamo come T.

**Find**: prende una copia di iteratori first e last è il Elem di tipo T passato per riferimento, se esiste riporta il primo elemento uguale.

**Find_if:** prende una coppia di iteratori, ed un predicato unario,  riporta l'elemento che soddisfa il predicato `pred( *first)` , altrimenti ritorna last;


**count**: ritorna un unisgned long, ogni volta che incontra elem, incrementa num e lo riporta alla fine.

count_if: se soddisfa il predicato, lo conta.

all_of: se almeno un elemento non soddisfa il predicato ritorna false, altrimenti ritorna true;
any_of: se almeno un elem soddisfa il predicato.

replace

replace_if: prende due iteratori, un predicato pred e un y di tipo T, se l'elemento puntato da first soddisfa il predicato, riscrive l'elemento puntato con y.

copy : prende 2 iteratori, e 1 iteratore output,  copia all'interno di out gli elementi puntati da first fino a last, poi incrementa out per avanzare.

copy_if

for_each

is_sorted prende due forward iterator è un cmp che compara due elementi per vedere se la lista è ordinata.

