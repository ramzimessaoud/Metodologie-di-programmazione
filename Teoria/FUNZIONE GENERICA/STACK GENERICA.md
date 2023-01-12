lezione 12

per dire che è uno stack generico, aggiungingiamo 
template `<typename T>`  ed andiamo a sostituire tutti gli elem con T 

gli stack di tipo T non hanno nessun acesso agli stack di tipo U, sono classi completamente diverse, quindi tutti i costruttori di copia, di aseegnamento, gli swap, vanno fatti da stack dello stesso tipo.


sostituiamo i costruttori da Stack a` Stack<T>` aggiungendo template `<typename T>` se si trovano all'esterno della classe (out of line).

aggiungiamo typename davanti alle variabili di ritorno:
```
template <typename T>
inline  `typename` Stack<T>::value_type&
Stack<T>::top() {
  assert(size_ > 0);
  return vec_[size_ - 1];
}
```

dopo di che andiamo ad aggiungere anche `Stack<T>` anche in stack.cc

