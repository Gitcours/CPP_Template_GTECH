# Templates en C++

## 1. Introduction aux Templates

Les **templates** en C++ permettent d'écrire du code générique, réutilisable et indépendant du type des données manipulées. Ils sont particulièrement utiles pour implémenter des structures de données et des algorithmes applicables à plusieurs types.

L'utilisation des templates évite la duplication de code en permettant d'écrire une seule version d'une fonction ou d'une classe qui peut fonctionner avec plusieurs types de données. Cela améliore la maintenabilité et la flexibilité du code.

Deux principaux types de templates existent en C++ :
- **Les templates de fonctions**, qui permettent de créer des fonctions génériques.
- **Les templates de classes**, qui permettent de définir des classes génériques.

### Pourquoi utiliser les templates ?
1. **Réduction de la redondance** : On évite d'écrire plusieurs versions de la même fonction ou classe pour différents types de données.
2. **Meilleure maintenabilité** : Une seule modification dans le template affecte tous les types supportés.
3. **Généricité et flexibilité** : Permet de manipuler différents types sans modifier le code source.
4. **Utilisation dans les bibliothèques standard** : La STL (Standard Template Library) repose sur l'utilisation intensive des templates.

---

## 2. Templates de Fonctions

Les **templates de fonctions** permettent d'écrire une fonction unique qui peut travailler avec plusieurs types de données. Plutôt que d'écrire plusieurs versions d'une même fonction pour différents types, on peut utiliser un template de fonction pour les généraliser.

### Définition d'un template de fonction

Un template de fonction est défini en utilisant le mot-clé `template` suivi d'un paramètre de type entre chevrons `<>`.

```cpp
#include <iostream>

// Définition d'un template de fonction
template <typename T>
T maximum(T a, T b) {
    return (a > b) ? a : b;
}

int main() {
    std::cout << "Max(10, 20) = " << maximum(10, 20) << std::endl;
    std::cout << "Max(5.5, 2.2) = " << maximum(5.5, 2.2) << std::endl;
    return 0;
}
```

### Explication détaillée :
- `template <typename T>` : Déclare `T` comme un type générique qui sera déterminé lors de l'appel à la fonction.
- `T maximum(T a, T b)` : Définit une fonction qui prend deux paramètres de type `T` et retourne le maximum des deux.
- Le compilateur génère automatiquement une version spécifique de la fonction pour chaque type utilisé.

### Utilisation de plusieurs types
Il est possible d'avoir plusieurs paramètres génériques :
```cpp
template <typename T1, typename T2>
void afficher(T1 a, T2 b) {
    std::cout << a << " et " << b << std::endl;
}
```

### Exercice : Calcul des points de dégâts
**Objectif** : Créer une fonction générique `calculerDegats` qui prend deux valeurs (attaque et défense) et retourne les dégâts infligés selon la formule `attaque - défense`.

---

## 3. Templates de Classes

Les **templates de classes** permettent de créer des classes génériques pouvant travailler avec différents types de données. Cela est particulièrement utile pour les structures de données comme les listes, les piles, et les tableaux dynamiques.

### Définition d'un template de classe

```cpp
#include <iostream>

template <typename T>
class Personnage {
private:
    T pointsDeVie;
public:
    Personnage(T pv) : pointsDeVie(pv) {}
    void afficherPV() {
        std::cout << "Points de vie: " << pointsDeVie << std::endl;
    }
};

int main() {
    Personnage<int> guerrier(100);
    Personnage<double> mage(75.5);
    
    guerrier.afficherPV();
    mage.afficherPV();
    return 0;
}
```

### Exercice : Gestion des statistiques d'un personnage
**Objectif** : Créer une classe `Statistiques` qui contient des valeurs génériques pour la force, l'agilité et l'intelligence. Modifier la classe `Personnage` afin d'ajouter un objet `Statistiques` à celle-ci lors de sa création.

---

## 4. Spécialisation des Templates

Il est possible de **spécialiser un template** pour un type particulier lorsque le comportement général ne convient pas.

### Définition d'une spécialisation
```cpp
#include <iostream>

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
};


int main() {
    Objet<int> potion("Potion de vie", 50);
    Objet<double> gemme("Gemme magique", 99.99);
    
    potion.afficher();
    gemme.afficher();
    return 0;
}
```

### Exercice : Création d'un coffre aux trésors
**Objectif** : Créer une classe `Coffre` qui stocke un objet de type générique et une quantité.

---

## 5. Exercice Final : Système d'Inventaire

**Objectif** : Créer un système d'inventaire générique qui peut contenir des objets de différents types. Notre `Personnage` dispose maintenant d'un inventaire, faites les modifications nécessaires.

```cpp
#include <iostream>

template <typename T>
class Inventaire {
private:
    T objets[5];
    int index;
public:
    Inventaire() : index(0) {}
    void ajouterObjet(T objet) {
        if (index < 5) {
            objets[index++] = objet;
        }
    }
    void afficherInventaire() {
        for (int i = 0; i < index; ++i) {
            std::cout << "Objet " << i+1 << ": " << objets[i] << std::endl;
        }
    }
};
```

---

## 6. Conclusion

Les templates en C++ permettent d'écrire du code générique et réutilisable. Ils sont essentiels pour implémenter des structures de données et des algorithmes indépendants du type des données manipulées.
