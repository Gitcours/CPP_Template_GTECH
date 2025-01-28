### **Exercice 1 : Calcul des points de dégâts**
**Consigne** : Créer une fonction générique `calculerDegats` qui prend deux valeurs (attaque et défense) et retourne les dégâts infligés selon la formule `attaque - défense`.

**Correction :**
```cpp
#include <iostream>

template <typename T>
T calculerDegats(T attaque, T defense) {
    return (attaque > defense) ? (attaque - defense) : 0; // Évite les dégâts négatifs
}

int main() {
    std::cout << "Dégâts (50, 20) = " << calculerDegats(50, 20) << std::endl;
    std::cout << "Dégâts (30.5, 10.2) = " << calculerDegats(30.5, 10.2) << std::endl;
    std::cout << "Dégâts (5, 10) = " << calculerDegats(5, 10) << std::endl; // Doit afficher 0
    return 0;
}
```
✅ **Correction apportée :** Gestion des dégâts négatifs (on s'assure que le résultat ne soit pas inférieur à 0).

---

### **Exercice 2 : Gestion des statistiques d'un personnage**
**Consigne** : Créer une classe `Statistiques` qui contient des valeurs génériques pour la force, l'agilité et l'intelligence. Modifier la classe `Personnage` afin d'ajouter un objet `Statistiques` à celle-ci lors de sa création.

**Correction :**
```cpp
#include <iostream>

template <typename T>
class Statistiques {
public:
    T force, agilite, intelligence;
    
    Statistiques(T f, T a, T i) : force(f), agilite(a), intelligence(i) {}
    
    void afficherStats() {
        std::cout << "Force: " << force << ", Agilité: " << agilite 
                  << ", Intelligence: " << intelligence << std::endl;
    }
};

template <typename T>
class Personnage {
private:
    T pointsDeVie;
    Statistiques<T> stats;
public:
    Personnage(T pv, T f, T a, T i) : pointsDeVie(pv), stats(f, a, i) {}

    void afficher() {
        std::cout << "Points de vie: " << pointsDeVie << std::endl;
        stats.afficherStats();
    }
};

int main() {
    Personnage<int> guerrier(100, 20, 15, 5);
    Personnage<double> mage(75.5, 5.2, 10.3, 30.7);

    guerrier.afficher();
    mage.afficher();
    return 0;
}
```
✅ **Correction apportée :** Ajout d'une classe `Statistiques` et intégration correcte dans `Personnage`.

---

### **Exercice 3 : Création d'un coffre aux trésors**
**Consigne** : Créer une classe `Coffre` qui stocke un objet de type générique et une quantité.

**Correction :**
```cpp
#include <iostream>
#include <string>

// Définition de la classe Objet
template <typename T>
class Objet {
private:
    std::string nom;
    T valeur;
public:
    Objet(std::string n, T v) : nom(n), valeur(v) {}

    void afficher() {
        std::cout << "Objet: " << nom << " - Valeur: " << valeur << std::endl;
    }

    std::string getNom() const { return nom; }
    T getValeur() const { return valeur; }
};

// Spécialisation pour les objets de type std::string
template <>
class Objet<std::string> {
private:
    std::string nom;
    std::string description;
public:
    Objet(std::string n, std::string desc) : nom(n), description(desc) {}

    void afficher() {
        std::cout << "Objet spécial: " << nom << " - Description: " << description << std::endl;
    }

    std::string getNom() const { return nom; }
    std::string getDescription() const { return description; }
};

// Classe Coffre
template <typename T>
class Coffre {
private:
    Objet<T> objet;
    int quantite;
public:
    Coffre(Objet<T> obj, int q) : objet(obj), quantite(q) {}

    void afficher() {
        std::cout << "Coffre contenant " << quantite << " x ";
        objet.afficher();
    }
};

int main() {
    Objet<int> potion("Potion de soin", 50);
    Objet<std::string> grimoire("Grimoire ancien", "Un livre rempli de sorts oubliés");

    Coffre<int> coffrePotions(potion, 5);
    Coffre<std::string> coffreLivres(grimoire, 2);

    coffrePotions.afficher();
    coffreLivres.afficher();

    return 0;
}

```
✅ **Correction apportée :** Ajout d'une classe `Coffre` avec gestion de la quantité et d'un type générique.

---

### **Exercice 4 : Système d'Inventaire**
**Consigne** : Créer un système d'inventaire générique qui peut contenir des objets de différents types. Notre `Personnage` dispose maintenant d'un inventaire.

**Correction :**
```cpp
#include <iostream>
#include <vector>
#include <string>
#include <memory>  // Pour std::unique_ptr

// Classe de base pour les objets
class ObjetBase {
public:
    virtual void afficher() const = 0;  // Méthode virtuelle pure
    virtual ~ObjetBase() {}  // Destructeur virtuel pour permettre une suppression correcte
};

// Classe Objet pour un type générique
template <typename T>
class Objet : public ObjetBase {
private:
    std::string nom;
    T valeur;
public:
    Objet(std::string n, T v) : nom(n), valeur(v) {}

    void afficher() const override {
        std::cout << "Objet: " << nom << " - Valeur: " << valeur << std::endl;
    }

    std::string getNom() const { return nom; }
    T getValeur() const { return valeur; }
};

// Classe Objet pour des objets de type std::string (spécialisation de l'objet)
template <>
class Objet<std::string> : public ObjetBase {
private:
    std::string nom;
    std::string description;
public:
    Objet(std::string n, std::string desc) : nom(n), description(desc) {}

    void afficher() const override {
        std::cout << "Objet spécial: " << nom << " - Description: " << description << std::endl;
    }

    std::string getNom() const { return nom; }
    std::string getDescription() const { return description; }
};

// Classe Inventaire avec des objets de types différents
class Inventaire {
private:
    std::vector<std::unique_ptr<ObjetBase>> objets;  // Conteneur de pointeurs sur ObjetBase
public:
    // Ajouter un objet de type quelconque (polymorphisme)
    template <typename T>
    void ajouter(const Objet<T>& obj) {
        objets.push_back(std::make_unique<Objet<T>>(obj));  // Ajouter un objet dans le conteneur
    }

    void afficher() const {
        std::cout << "Inventaire : " << std::endl;
        for (const auto& obj : objets) {
            obj->afficher();  // Appel polymorphique de la méthode afficher
        }
        std::cout << std::endl;
    }
};

// Classe Personnage
class Personnage {
private:
    std::string nom;
    Inventaire inventaire;
public:
    Personnage(std::string n) : nom(n) {}

    void ajouterObjet(const ObjetBase& obj) {
        inventaire.ajouter(obj);  // Ajouter un objet générique à l'inventaire
    }

    void afficherInventaire() const {
        std::cout << "Inventaire de " << nom << ":" << std::endl;
        inventaire.afficher();
    }
};

int main() {
    Personnage guerrier("Arthur");
    
    Objet<int> epee("Épée légendaire", 100);
    Objet<int> bouclier("Bouclier magique", 75);
    Objet<std::string> grimoire("Grimoire ancien", "Livre rempli de sorts puissants");

    // Ajouter des objets de types différents dans l'inventaire du guerrier
    guerrier.ajouterObjet(epee);
    guerrier.ajouterObjet(bouclier);
    guerrier.ajouterObjet(grimoire);

    // Afficher l'inventaire du guerrier
    guerrier.afficherInventaire();

    return 0;
}

```
