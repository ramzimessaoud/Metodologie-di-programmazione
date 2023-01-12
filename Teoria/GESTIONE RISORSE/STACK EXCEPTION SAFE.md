lezione 10

scriveremo una classe stack exception safe nothrow di tipo strong con correta gestione delle risorse anche in caso di eccezioni.

std::size_t; 
alias nelle librerie standart per le dimensioni, dobbiamo includere ``<cstddef> ``

usiamo value_type e size_type per generalizzare, per la sorpresa minima

il costruttore e asseganmento per spostamento hanno la scrite ``noexcept``  per dire che sono a livello nothrow, non lanciano eccezioni 

sarebbe utile avere un metodo ``swap`` per scambiare risorse, anche questo noexcept

push, pop, is_empty scritte in modo classiche.

se vogliamo sapere cosa ce in cima allo stack usiamo il metodo top, ne abbiamo due, uno in acesso in sola lettura con il "const", è uno sia in scrittura sia in lettura. 

!! noi assumiamo che ci sia un elemento in cima"" 

check_inv un booleano che controlla che la invariante sia valida


poi andiamo nella parte privata:

abbiamo un puntatore alla memoria che punta all'inzio dell'array, la capacità che sarebbe la capacità attuale, e size la dimensione attuale.


andaimo a vedere come sono implementati i metodi:

il costruttore:
````
Stack::Stack(const size_type capacity)
  : vec_(nullptr), capacity_(capacity), size_(0) {
  if (capacity_ > 0)
    vec_ = new value_type[capacity_];
  assert(check_inv());
}
````

inizialliza i dati membro, in ordine in cui sono elencati e li inizializza vuoti, non allocano niente. 
se la capacity è maggiore di 0, alloco un array di risorse con la new sul puntatore vec_ che riceve l'indirizzo. poi controllo l'invariante di classe.

qui l'unica cosa a rischio è la new di array, se una di loro falisce o non ho abbastanza memoria, usciamo in modalità eccezionale. 


all'interno del file stack.cc
````
bool
Stack::check_inv() const {
  if (capacity_ == 0 && vec_ != nullptr) {
#ifndef NDEBUG
    std::cerr << "Lo Stack è dichiarato avere una capacità "
      "nulla ma ha risorse allocate!" << std::endl;
#endif // NDEBUG
    return false;
  }
  if (size_ > capacity_) {
#ifndef NDEBUG
    std::cerr << "Lo Stack è dichiarato avere una dimensione "
	      << "superiore alla capacità!" << std::endl;
#endif // NDEBUG
    return false;
  }
  if (capacity_ > 0 && vec_ == nullptr) {
#ifndef NDEBUG
    std::cerr << "Non vi sono risorse allocate per lo Stack!"
	      << std::endl;
#endif // NDEBUG
    return false;
  }
  // Tutto ok.
  return true;
}
````

la invariante di classe controla che effetivamente se la capacità è 0 non devo aver allocato risorse, in caso contrario, lancio errore.

il secondo controllo, controllo che la dimensione non abbia superato la capacità

il terzo controolo che se la capacità è maggiore di 0, devo aver allocato risorse.

se è tutto ok, ritorno true.


````
inline
Stack::~Stack() {
  delete[] vec_;
}
````
distruttore, fa la delete sul puntatore all'array di risorse.