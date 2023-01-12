Dependency Inversion Principle

Dependency Inversion Principle (DIP): Questo principio afferma che i moduli di alto livello non dovrebbero dipendere da moduli di basso livello, ma entrambi dovrebbero dipendere da un'interfaccia abstra. Violazione: Una classe di alto livello che dipende direttamente da una classe di basso livello.

Esempio di violazione di DIP:
```
class Database { 
public: 
    void Connect() { 
        // Connette al database 
    } 
    void ExecuteQuery() { 
        // Esegue la query 
    } 
}; 

class BusinessLogic { 
private: 
    Database db; 
public: 
    void ProcessData() { 
        db.Connect(); 
        db.ExecuteQuery(); 
    } 
};
```
ho violato il principio DIP facendo in modo che la classe BusinessLogic dipenda direttamente dalla classe Database.





```
// Codice che rispetta DIP
class IDatabase { 
public: 
    virtual void Connect() = 0; 
    virtual void ExecuteQuery() = 0; 
}; 

class Database : public IDatabase { 
public: 
    void Connect() { 
        // Connette al database 
    } 
    void ExecuteQuery() { 
        // Esegue la query 
    } 
}; 

class BusinessLogic { 
private: 
    IDatabase* db; 
public: 
    BusinessLogic(IDatabase* db) { 
        this->db = db; 
    } 
    void ProcessData() { 
        db->Connect(); 
        db->ExecuteQuery(); 
    } 
};
```
ho rispettato il principio DIP fornendo un'interfaccia IDatabase e facendo in modo che la classe BusinessLogic dipenda da quell'interfaccia. Ciò significa che la classe BusinessLogic non dipende direttamente dalla classe Database.



altro esempio:

Violazione del principio DIP: 
```
class Database {
public:
    void writeData();
    void readData();
};

class User {
private:
    Database db;
public:
    void doSomething() {
        db.writeData();
        db.readData();
    }
};
```
Correzione del principio DIP: 
```
class Database {
public:
    virtual void writeData() = 0;
    virtual void readData() = 0;
};

class User {
private:
    Database& db;
public:
    User(Database& db) : db(db) {}
    void doSomething() {
        db.writeData();
        db.readData();
    }
};
```

**Nella violazione** del principio DIP, abbiamo una classe Database con due funzioni writeData() e readData(), e una classe User con una funzione doSomething(). La funzione doSomething() nella classe User richiama entrambe le funzioni writeData() e readData() dalla classe Database. Ciò viola il principio DIP poiché una classe non dovrebbe dipendere da un'altra classe.

**Nella correzione** del principio DIP, abbiamo ancora una classe Database con due funzioni writeData() e readData(), ma queste sono ora funzioni virtuali con la parola chiave = 0. Abbiamo anche una classe User con un costruttore che accetta un riferimento a un oggetto Database e una funzione doSomething(). Questa volta, la funzione doSomething() non crea un'istanza di Database, ma prende una referenza a un'istanza esistente. Ciò rispetta il principio DIP poiché la classe User non dipende più dalla classe Database.

**DIP**