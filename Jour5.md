# **Cours détaillé sur les Design Patterns en C++**  

Les **design patterns** (ou motifs de conception) sont des solutions générales aux problèmes récurrents en programmation. Ils permettent d'écrire un code plus **structuré, réutilisable et maintenable**.  

Ce cours est structuré en trois grandes catégories de **design patterns** :  
1. **Les patterns de création** → Gèrent l'instanciation des objets.  
2. **Les patterns structurels** → Organisent les classes et objets pour former des structures plus efficaces.  
3. **Les patterns comportementaux** → Gèrent les interactions entre objets.  

Chaque section présentera un pattern avec une **explication claire et un exemple détaillé en C++**.  

---

# **1. Patterns de Création**  

Ces patterns permettent de **contrôler la création des objets** pour améliorer la flexibilité et l'extensibilité du code.  

## **1.1. Singleton**  

Le **Singleton** garantit qu'une classe ne possède **qu'une seule instance** et fournit un point d'accès global à cette instance.  

### **Problème :**  
- Parfois, un objet unique est nécessaire (ex : gestionnaire de logs, gestionnaire de base de données).  

### **Solution :**  
- On crée une **classe avec une instance unique et un accès global**.  

#### **Exemple de Singleton en C++ :**  
```cpp
#include <iostream>

using namespace std;

class Singleton {
private:
    static Singleton* instance; // Pointeur vers l'unique instance
    Singleton() {}  // Constructeur privé

public:
    static Singleton* getInstance() {
        if (instance == nullptr) {
            instance = new Singleton();
        }
        return instance;
    }

    void showMessage() {
        cout << "Instance unique du Singleton !" << endl;
    }
};

// Initialisation de l'instance unique
Singleton* Singleton::instance = nullptr;

int main() {
    Singleton* s1 = Singleton::getInstance();
    s1->showMessage();

    Singleton* s2 = Singleton::getInstance();
    cout << (s1 == s2) << endl;  // Affiche 1, prouvant qu'il n'y a qu'une seule instance

    return 0;
}
```
### **Explication du code :**  
1. **L’instance unique** est stockée dans `static Singleton* instance`.  
2. **Le constructeur est privé** pour empêcher la création d’instances externes.  
3. **`getInstance()` crée l’instance si elle n’existe pas encore** et retourne toujours la même.  
4. **On vérifie que `s1` et `s2` pointent vers la même instance.**  

> **Avantage :** Contrôle global sur une ressource unique.  
> **Inconvénient :** Peut poser des problèmes en **programmation multi-thread** (à sécuriser avec `std::mutex`).  

---

## **1.2. Factory Method**  

Le **Factory Method** est un pattern qui délègue la création d’objets à une **méthode spécialisée** plutôt qu’à un constructeur.  

### **Problème :**  
- On veut créer des objets sans exposer leur logique interne ni dépendre directement de leur classe.  

### **Solution :**  
- On utilise une **méthode abstraite qui fabrique les objets**.  

#### **Exemple en C++ :**  
```cpp
#include <iostream>

using namespace std;

// Interface de base
class Product {
public:
    virtual void use() = 0;
    virtual ~Product() {}
};

// Produit concret 1
class ConcreteProductA : public Product {
public:
    void use() override {
        cout << "Utilisation du produit A" << endl;
    }
};

// Produit concret 2
class ConcreteProductB : public Product {
public:
    void use() override {
        cout << "Utilisation du produit B" << endl;
    }
};

// Factory Method
class Factory {
public:
    static Product* createProduct(const string& type) {
        if (type == "A") return new ConcreteProductA();
        else if (type == "B") return new ConcreteProductB();
        return nullptr;
    }
};

int main() {
    Product* p1 = Factory::createProduct("A");
    p1->use();

    Product* p2 = Factory::createProduct("B");
    p2->use();

    delete p1;
    delete p2;

    return 0;
}
```
### **Explication du code :**  
1. **`Product` est une interface** représentant le produit à créer.  
2. **`ConcreteProductA` et `ConcreteProductB` sont des implémentations concrètes.**  
3. **`Factory::createProduct()` fabrique un produit en fonction du type demandé.**  

> **Avantage :** Facilite l'ajout de nouveaux produits sans modifier le code existant.  
> **Inconvénient :** Peut devenir complexe avec de nombreux types de produits.  

---

# **2. Patterns Structurels**  

Ces patterns concernent **la composition et l'organisation des classes** pour obtenir des architectures logicielles plus flexibles.

---

## **2.1. Adapter**  

Le **pattern Adapter** permet de **convertir une interface existante** en une autre interface attendue.  

### **Problème :**  
- Une classe existante ne correspond pas à l'interface dont on a besoin.  

### **Solution :**  
- On crée une classe intermédiaire (`Adapter`) qui fait la conversion.  

#### **Exemple en C++ :**  
```cpp
#include <iostream>

using namespace std;

// Interface existante
class OldSystem {
public:
    void legacyMethod() {
        cout << "Ancien système en cours d'exécution" << endl;
    }
};

// Interface cible
class NewSystem {
public:
    virtual void newMethod() = 0;
};

// Adapter qui transforme l'ancien système en un nouveau système compatible
class Adapter : public NewSystem {
private:
    OldSystem* oldSystem;

public:
    Adapter(OldSystem* os) : oldSystem(os) {}

    void newMethod() override {
        oldSystem->legacyMethod(); // Appelle la méthode de l'ancien système
    }
};

int main() {
    OldSystem* oldSys = new OldSystem();
    NewSystem* adapter = new Adapter(oldSys);

    adapter->newMethod(); // Appelle l'ancien système via l'adaptateur

    delete oldSys;
    delete adapter;

    return 0;
}
```
### **Explication du code :**  
1. **`OldSystem` représente un système existant** avec une interface obsolète.  
2. **`NewSystem` est l’interface souhaitée**.  
3. **`Adapter` convertit `OldSystem` en `NewSystem` en appelant `legacyMethod()`**.  

> **Avantage :** Permet d'intégrer du **code hérité sans modification**.  
> **Inconvénient :** Ajoute une couche d’indirection.  

---

# **3. Patterns Comportementaux**  

Ces patterns se concentrent sur **les interactions entre objets et la gestion des responsabilités**.

---

## **3.1. Observer**  

Le **pattern Observer** permet à **plusieurs objets (observateurs) de réagir aux changements d’un objet principal (sujet)**.  

### **Problème :**  
- Un objet doit informer dynamiquement plusieurs autres objets lorsqu’il change d’état.  

### **Solution :**  
- On utilise un **système d’abonnement** entre un `Sujet` et des `Observateurs`.  

#### **Exemple en C++ :**  
```cpp
#include <iostream>
#include <vector>

using namespace std;

class Observer {
public:
    virtual void update(int state) = 0;
};

class Subject {
private:
    vector<Observer*> observers;
    int state;

public:
    void addObserver(Observer* obs) {
        observers.push_back(obs);
    }

    void setState(int newState) {
        state = newState;
        notify();
    }

    void notify() {
        for (auto* obs : observers) {
            obs->update(state);
        }
    }
};

class ConcreteObserver : public Observer {
public:
    void update(int state) override {
        cout << "État mis à jour : " << state << endl;
    }
};

int main() {
    Subject sujet;
    ConcreteObserver obs1, obs2;

    sujet.addObserver(&obs1);
    sujet.addObserver(&obs2);

    sujet.setState(10);

    return 0;
}
```
> **Avantage :** Permet une communication flexible entre objets.  
> **Inconvénient :** Peut devenir complexe si mal géré.  

---

## **3.2. Visitor**  
**Objectif** : Permet d’ajouter de nouvelles opérations sur des objets sans modifier leur structure.  

### **Problème**  
On a une hiérarchie de classes (`Circle`, `Rectangle`) et on veut ajouter des fonctionnalités comme **calculer l’aire**, **afficher la forme**, **exporter en JSON**, etc.  

**Solution naïve** : Ajouter des méthodes dans `Circle` et `Rectangle`  
**Pattern Visitor** : On déplace ces opérations dans une classe externe.  

---


```cpp

#include <iostream>

using namespace std;

class Circle;
class Rectangle;

// Interface du visiteur
class Visitor {
public:
    virtual void visit(Circle& c) = 0;
    virtual void visit(Rectangle& r) = 0;
};

class Shape {
public:
    virtual void accept(Visitor& visitor) = 0;
    virtual ~Shape() = default;
};

class Circle : public Shape {
public:
    void accept(Visitor& visitor) override {
        visitor.visit(*this);
    }
};

class Rectangle : public Shape {
public:
    void accept(Visitor& visitor) override {
        visitor.visit(*this);
    }
};



class DrawVisitor : public Visitor {
public:
    void visit(Circle& c) override {
        cout << "Dessiner un cercle" << endl;
    }

    void visit(Rectangle& r) override {
        cout << "Dessiner un rectangle" << endl;
    }
};

class AreaVisitor : public Visitor {
public:
    void visit(Circle& c) override {
        cout << "Calcul de l’aire d’un cercle" << endl;
    }

    void visit(Rectangle& r) override {
        cout << "Calcul de l’aire d’un rectangle" << endl;
    }
};



int main() {
    Circle circle;
    Rectangle rectangle;

    DrawVisitor drawVisitor;
    AreaVisitor areaVisitor;

    // Dessiner les formes
    circle.accept(drawVisitor);
    rectangle.accept(drawVisitor);

    // Calculer l'aire des formes
    circle.accept(areaVisitor);
    rectangle.accept(areaVisitor);

    return 0;
}
```
#### **Sortie du programme :**  
```
Dessiner un cercle
Dessiner un rectangle
Calcul de l’aire d’un cercle
Calcul de l’aire d’un rectangle
```

`accept(Visitor& visitor)` permet d’envoyer l’objet courant au `Visitor`.  
**Avantage** : On peut créer un `JsonExportVisitor`, `TransformVisitor`, etc., **sans modifier les classes `Circle` et `Rectangle`**.  

**Ces patterns sont essentiels pour concevoir du code robuste et modulaire en C++.**
