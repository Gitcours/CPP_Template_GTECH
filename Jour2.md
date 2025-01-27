# **Cours C++ : La Gestion des Erreurs**  

La gestion des erreurs est un aspect fondamental du développement logiciel en C++. Elle permet de rendre les programmes plus robustes et plus fiables en anticipant les comportements imprévus (division par zéro, fichier manquant, accès mémoire invalide, etc.).  

En C++, il existe plusieurs techniques pour gérer les erreurs :  

1. **Les codes de retour**  
2. **L’utilisation de `errno` et `perror`**  
3. **Le mécanisme des exceptions (`try`, `catch`, `throw`)**  

---

## **1. Gestion des erreurs avec les codes de retour**  

L'une des méthodes les plus simples pour signaler une erreur consiste à utiliser une valeur de retour spéciale. On définit des conventions pour distinguer une exécution réussie d’une erreur.  

### **Exemple :**  
```cpp
#include <iostream>

int division(int a, int b, int& result) {
    if (b == 0) {
        return -1; // Code d'erreur indiquant une division par zéro
    }
    result = a / b;
    return 0; // Code indiquant une exécution réussie
}

int main() {
    int res;
    if (division(10, 0, res) != 0) {
        std::cerr << "Erreur : Division par zéro !" << std::endl;
    } else {
        std::cout << "Résultat : " << res << std::endl;
    }
    return 0;
}
```
### **Explication :**  
- La fonction `division` renvoie `-1` si une erreur est détectée (division par zéro).  
- Si l'opération réussit, elle retourne `0` et affecte le résultat à `result`.  
- Dans `main`, on teste la valeur de retour pour détecter les erreurs.  

### **Avantages :**  
✅ Simple à implémenter et rapide à exécuter.  
✅ Pas de surcharge mémoire supplémentaire.  

### **Inconvénients :**  
❌ Peut être oublié par le programmeur (on peut ne pas vérifier le retour).  
❌ Ambiguïté possible si les valeurs de retour normales chevauchent les codes d’erreur.  
❌ La gestion devient compliquée lorsque plusieurs erreurs doivent être distinguées.  

---

## **2. Gestion des erreurs avec `errno` et `perror`**  

`errno` est une variable globale définie dans `<cerrno>`. Elle est mise à jour par certaines fonctions du système pour indiquer une erreur.  

### **Exemple :**  
```cpp
#include <iostream>
#include <cstdio>
#include <cerrno>
#include <cstring>

int main() {
    FILE* file = fopen("fichier_inexistant.txt", "r");
    if (!file) {
        std::cerr << "Erreur : " << strerror(errno) << std::endl;
    }
    return 0;
}
```
### **Explication :**  
- `fopen()` retourne `nullptr` si le fichier n’existe pas.  
- `errno` est automatiquement mis à jour avec le code d'erreur.  
- `strerror(errno)` affiche un message lisible associé à l'erreur.  

### **Alternative avec `perror()` :**  
```cpp
#include <iostream>
#include <cstdio>

int main() {
    FILE* file = fopen("fichier_inexistant.txt", "r");
    if (!file) {
        perror("Erreur d'ouverture du fichier");
    }
    return 0;
}
```
- `perror("Erreur")` affiche automatiquement un message d'erreur basé sur `errno`.  

### **Avantages :**  
✅ Standardisé et bien intégré aux appels système.  
✅ Utile pour les erreurs liées aux fichiers, à la mémoire ou au réseau.  

### **Inconvénients :**  
❌ `errno` est une variable globale, ce qui peut causer des problèmes en environnement multi-thread.  
❌ Ne fonctionne que pour certaines fonctions (celles qui modifient `errno`).  

---

## **3. Gestion des erreurs avec les exceptions (`try-catch`)**  

C++ propose un mécanisme de gestion des erreurs avec les **exceptions**, permettant de séparer la logique normale de la gestion des erreurs.  

### **Syntaxe de base :**  
```cpp
try {
    // Code qui peut lever une exception
} catch (type_exception& e) {
    // Code pour gérer l'exception
}
```

### **Exemple :**  
```cpp
#include <iostream>
#include <stdexcept>

int division(int a, int b) {
    if (b == 0) {
        throw std::runtime_error("Erreur : Division par zéro !");
    }
    return a / b;
}

int main() {
    try {
        std::cout << "Résultat : " << division(10, 0) << std::endl;
    } catch (const std::exception& e) {
        std::cerr << "Exception attrapée : " << e.what() << std::endl;
    }
    return 0;
}
```
### **Explication :**  
1. Si `b == 0`, on **lève une exception** avec `throw std::runtime_error("message")`.  
2. Le `catch` capture l’exception et affiche le message d'erreur.  
3. Si aucune exception n'est levée, la division s'exécute normalement.  

### **Avantages :**  
✅ Sépare clairement le code normal de la gestion d'erreur.  
✅ Permet de propager l'erreur sans retour de valeur explicite.  
✅ Peut encapsuler des informations détaillées dans l’exception.  

### **Inconvénients :**  
❌ Coût en performance (lever et attraper une exception est plus lent qu’un code de retour).  
❌ Moins lisible si mal utilisé.  
❌ Tous les types ne sont pas exceptionnels : éviter d’utiliser `throw` pour des erreurs triviales.  

---

## **4. Bonnes Pratiques pour la Gestion des Erreurs**  

### **🔹 Quand utiliser les codes de retour ?**  
✅ Pour des erreurs fréquentes et attendues (exemple : échec d’ouverture d’un fichier).  
✅ Lorsque la gestion d’erreur est simple et qu’on veut éviter les exceptions.  

### **🔹 Quand utiliser `errno` et `perror` ?**  
✅ Pour des erreurs système standard (fichiers, mémoire, réseau).  
✅ Lorsque les fonctions utilisées mettent automatiquement à jour `errno`.  

### **🔹 Quand utiliser les exceptions ?**  
✅ Pour des erreurs graves et inattendues (exemple : allocation mémoire échouée).  
✅ Lorsqu’il faut propager une erreur à travers plusieurs niveaux d’appels.  
✅ Si on veut séparer proprement le code fonctionnel et la gestion d’erreur.  

### **🔹 Éviter les erreurs courantes :**  
❌ **Ne pas ignorer les erreurs** : Toujours traiter un code de retour ou une exception.  
❌ **Ne pas abuser des exceptions** : Elles doivent être réservées aux cas vraiment exceptionnels.  
❌ **Toujours capturer les exceptions par référence** (`catch (const std::exception& e)`).  
❌ **Gérer proprement la libération des ressources** (utiliser des pointeurs intelligents pour éviter les fuites mémoire).  

---

## **5. Comparatif des Méthodes de Gestion des Erreurs**  

| Méthode            | Avantages | Inconvénients | Utilisation idéale |
|--------------------|----------|--------------|---------------------|
| **Codes de retour** | Simple, rapide | Facile à oublier, ambiguïté possible | Erreurs fréquentes et attendues |
| **`errno` et `perror`** | Standardisé, adapté aux erreurs système | Global, limité à certaines fonctions | Erreurs système (fichiers, mémoire, réseau) |
| **Exceptions** | Séparation propre, propagation facile | Coût en performance, complexité | Erreurs critiques et inattendues |

---

## **6. Exercices**

Pour mieux comprendre et maîtriser la gestion des erreurs en C++, voici une série d'exercices progressifs.  

---

## **Exercice 1 : Gestion des erreurs avec un code de retour**  

### **Objectif :**  
Écrire une fonction `int lireEntier(int& valeur)` qui demande à l’utilisateur de saisir un entier.  
- Si l’utilisateur entre une valeur valide, la fonction retourne `0` et met à jour `valeur`.  
- Si l’entrée est invalide (exemple : un texte), la fonction retourne `-1` sans modifier `valeur`.  

### **Exemple d’exécution attendue :**  
```
Entrez un entier : abc
Erreur : entrée invalide !
Entrez un entier : 42
Vous avez saisi : 42
```

---

## **Exercice 2 : Gestion des erreurs avec `errno` et `perror`**  

### **Objectif :**  
Écrire un programme qui tente d’ouvrir un fichier nommé `"data.txt"`.  
- Si le fichier est trouvé, afficher `"Fichier ouvert avec succès"`.  
- Si le fichier est introuvable, afficher un message d’erreur avec `perror()`.  

### **Exemple d’exécution attendue :**  
```
Erreur : Aucun fichier ou dossier de ce type
```
---

## **Exercice 3 : Gestion des erreurs avec les exceptions**  

### **Objectif :**  
Créer une fonction `int division(int a, int b)` qui :  
- Retourne `a / b` si `b ≠ 0`.  
- Lève une exception `std::runtime_error` si `b == 0`.  

Dans `main()`, tester la fonction avec `try-catch` et afficher `"Exception capturée : message"` en cas d’erreur.  

### **Exemple d’exécution attendue :**  
```
Exception capturée : Division par zéro !
```
---

## **Exercice 4 : Création d’un mini-système de gestion d’erreurs**  

### **Objectif :**  
Créer un programme qui gère l’inscription d’un utilisateur avec les contraintes suivantes :  
- L’utilisateur doit entrer un âge valide (≥ 18).  
- Lever une exception `std::invalid_argument` si l’âge est invalide.  
- Afficher un message d’erreur approprié en capturant l’exception dans `main()`.  

### **Exemple d’exécution attendue :**  
```
Entrez votre âge : 15
Erreur : âge non valide !
```
---

## **Exercice 5 : Gestion des erreurs avancée (combinaison de techniques)**  

### **Objectif :**  
Créer un programme qui demande à l’utilisateur d’entrer deux nombres et effectue la division :  
- Vérifier si l’entrée utilisateur est un nombre valide.  
- Vérifier si `b == 0` avant la division.  
- En cas d’erreur, afficher un message approprié et redemander une entrée.  
- Utiliser une combinaison de **codes de retour et exceptions** pour une gestion robuste.  

### **Exemple d’exécution attendue :**  
```
Entrez un nombre : abc
Erreur : entrée invalide !
Entrez un nombre : 10
Entrez un diviseur : 0
Erreur : Division par zéro !
Entrez un diviseur : 2
Résultat : 5
```
---
