# **Cours Avancé sur le C++ Moderne (C++17, C++20, C++23)**

---

## **1. C++17 : Améliorations Majeures**

C++17 apporte plusieurs optimisations qui simplifient le développement, réduisent les erreurs et améliorent la lisibilité du code.


---

### **1.1. std::optional : Représenter une Valeur Facultative**

Problème avant C++17

Avant l’introduction de std::optional, lorsqu’une fonction pouvait ne pas renvoyer de valeur valide, on avait deux approches courantes :

1. Utiliser une valeur spéciale (ex: -1 pour signaler une erreur).


2. Retourner un pointeur qui pouvait être nullptr.



Problèmes avec ces approches :

Les valeurs spéciales peuvent être ambiguës → Si -1 est une erreur, comment gérer une fonction qui peut légitimement retourner -1 ?

L’utilisation de pointeurs ajoute des risques → Déréférencement de nullptr possible si on oublie la vérification.


Solution avec std::optional

C++17 introduit std::optional, une classe qui encapsule une valeur qui peut être présente ou absente.

Exemple : Utilisation de std::optional

 ```cpp
#include <iostream>
#include <optional>

using namespace std;

// Fonction qui retourne un entier s'il est valide, sinon rien
optional<int> findElement(int index) {
    if (index >= 0 && index < 10) 
        return index * 2; // Retourne une valeur valide
    return nullopt;       // Indique l'absence de valeur
}

int main() {
    auto result = findElement(5);

    if (result.has_value()) { // Vérifie si une valeur est présente
        cout << "Valeur trouvée : " << result.value() << endl;
    } else {
        cout << "Aucune valeur trouvée" << endl;
    }

    return 0;
}
```

Explication du code :

1. Déclaration de std::optional<int> → Permet de stocker un entier ou nullopt.


2. Vérification avec has_value() → Évite les accès invalides.


3. Utilisation de value() → Accède directement à la valeur si elle existe.



> Avantage : Évite l’utilisation de pointeurs nullptr et améliore la robustesse du code.




---

### **1.2. union vs std::variant : Pourquoi variant est Plus Sûr**

Avant C++17, pour stocker plusieurs types dans une seule structure mémoire, on utilisait union. Cependant, union a plusieurs limitations.


---

#### **1.2.1. Problème avec union**

union permet de stocker un seul type à la fois, mais il n’offre aucune sécurité en termes de vérification de type.

Exemple : Utilisation d'un union

```cpp
#include <iostream>

using namespace std;

union Data {
    int i;
    double d;
    char str[20]; // Tableau de caractères
};

int main() {
    Data data;

    data.i = 42; // Stocke un entier
    cout << "Entier : " << data.i << endl;

    data.d = 3.14; // Remplace l'entier par un double
    cout << "Double : " << data.d << endl;

    // Problème : `data.i` est maintenant corrompu !
    cout << "Entier corrompu : " << data.i << endl;

    return 0;
}
```

Problèmes avec ce code :

1. Pas de vérification de type → On peut accidentellement lire un type incorrect.


2. Mémoire partagée → Stocker un double écrase la valeur de int.


3. Pas de gestion automatique des objets complexes → Impossible d'utiliser std::string directement.




---

#### **1.2.2. Solution avec std::variant**

C++17 introduit std::variant, qui est une version plus sûre de union.

Exemple : Utilisation de std::variant
```cpp
#include <iostream>
#include <variant>

using namespace std;

int main() {
    variant<int, double, string> data;

    data = 42;
    cout << "Entier : " << get<int>(data) << endl;

    data = 3.14;
    cout << "Double : " << get<double>(data) << endl;

    data = "Hello, world!";
    cout << "String : " << get<string>(data) << endl;

    return 0;
}
```
Explication du code :

1. Déclaration de variant<int, double, string> → Peut stocker un de ces trois types.


2. Attribution dynamique → data peut contenir un int, un double ou un string.


3. Utilisation de get<Type>(variant) → Vérifie et récupère la valeur en toute sécurité.



> Avantage : Garantit l'accès sécurisé aux valeurs et empêche la corruption de mémoire.




---

### **1.3. if constexpr : Exécution Conditionnelle à la Compilation**

#### **1.3.1. Problème avant if constexpr**

Avant C++17, pour adapter le comportement d'un template en fonction du type utilisé, nous devions nous appuyer sur SFINAE (enable_if), ce qui compliquait le code.

Exemple : Vérification du type avec enable_if
```cpp
#include <iostream>
#include <type_traits>

using namespace std;

template <typename T>
typename enable_if<is_integral<T>::value>::type printType(T value) {
    cout << "C'est un entier : " << value << endl;
}

template <typename T>
typename enable_if<!is_integral<T>::value>::type printType(T value) {
    cout << "Autre type : " << value << endl;
}

int main() {
    printType(10);
    printType(3.14);
    return 0;
}
```
Problèmes avec cette approche :

Code peu lisible → L’utilisation de enable_if rend le code difficile à comprendre.

Complexité inutile → Nécessite plusieurs spécialisations de template.



---

#### **1.3.2. Solution avec if constexpr**

Avec if constexpr, le compilateur peut supprimer le code inutile et rendre le template plus clair.

Exemple : Vérification du type avec if constexpr
```cpp
#include <iostream>
#include <type_traits>

using namespace std;

template <typename T>
void printType(T value) {
    if constexpr (is_integral_v<T>) {
        cout << "C'est un entier : " << value << endl;
    } else {
        cout << "Autre type : " << value << endl;
    }
}

int main() {
    printType(10);
    printType(3.14);
    return 0;
}
```
Explication du code :

1. Utilisation de if constexpr → Permet une évaluation conditionnelle à la compilation.


2. is_integral_v<T> → Vérifie si T est un entier (int, long, etc.).


3. Optimisation du compilateur → Supprime la branche inutile dans chaque cas.



> Avantage : Code plus clair, plus lisible et optimisé.




---

## **2. C++20 : Révolution du Langage**

C++20 est l’une des plus grandes mises à jour du langage. Il introduit des fonctionnalités qui améliorent la sécurité, la lisibilité et les performances.


---

### **2.1. Concepts : Contraindre les Templates pour un Code Plus Clair**

Avant C++20, les templates étaient très flexibles, mais cela pouvait entraîner des erreurs de compilation difficiles à comprendre.

Les concepts permettent de restreindre les types utilisés dans les templates, ce qui améliore la clarté et la sécurité du code.


---

#### **2.1.1. Problème avec les Templates sans Concepts**

Sans les concepts, si nous utilisons un mauvais type dans un template, l’erreur de compilation est difficile à comprendre.

Exemple : Template sans restriction
```cpp
#include <iostream>

using namespace std;

template <typename T>
T add(T a, T b) {
    return a + b;
}

int main() {
    cout << add(5, 10) << endl;
    cout << add("Hello", "World") << endl;  // Compilation réussie mais problème logique
}
```
Problèmes avec ce code :

Aucune contrainte sur T → add() accepte des int, double, string, etc.

Erreur difficile à détecter → Si T est une chaîne (string), l’addition + peut fonctionner mais est hors contexte pour une addition numérique.



---

#### **2.1.2. Solution avec concept**

Avec concept, on peut imposer des contraintes sur les types utilisés dans un template.

Exemple : Restriction d’un template avec concept
```cpp
#include <iostream>
#include <concepts>

using namespace std;

// Définition d'un concept : Seuls les types numériques sont autorisés
template <typename T>
concept Numeric = is_arithmetic_v<T>; 

template <Numeric T>
T add(T a, T b) {
    return a + b;
}

int main() {
    cout << add(5, 10) << endl; // OK
    // cout << add("Hello", "World"); // Erreur de compilation grâce au concept
}
```
Explication du code :

1. Déclaration du concept Numeric → Vérifie si T est un type numérique (int, double, etc.).


2. Utilisation de concept dans le template → Seuls les types conformes à Numeric sont autorisés.


3. Amélioration des erreurs de compilation → Si on passe un string, l'erreur est claire.



> Avantage : Les concepts permettent d'écrire des templates plus sûrs et plus lisibles.




---

### **2.2. Coroutines : Programmation Asynchrone Simplifiée**

#### **2.2.1. Problème avant C++20**

Avant C++20, écrire du code asynchrone nécessitait des threads (std::thread), des futures (std::future) ou des callbacks, ce qui compliquait le code et entraînait une gestion difficile des ressources.


---

2.2.2. Solution avec les Coroutines (co_await, co_yield)

Avec les coroutines, il est possible d'écrire du code asynchrone de manière linéaire et plus lisible.

Exemple : Coroutine Simple
```cpp
#include <iostream>
#include <coroutine>

using namespace std;

struct Task {
    struct promise_type {
        Task get_return_object() { return {}; }
        suspend_never initial_suspend() { return {}; }
        suspend_never final_suspend() noexcept { return {}; }
        void return_void() {}
        void unhandled_exception() { terminate(); }
    };
};

Task asyncFunction() {
    cout << "Début de la coroutine\n";
    co_await suspend_always{};  
    cout << "Coroutine reprise\n";
}

int main() {
    auto t = asyncFunction();
    cout << "Fin du main\n";
    return 0;
}
```
Explication du code :

1. Définition de Task → Permet d'utiliser la coroutine.


2. Utilisation de co_await suspend_always{} → Suspend l’exécution.


3. Le main continue sans attendre la coroutine.



> Avantage : Simplifie grandement l'écriture de code asynchrone.




---

## **3. C++23 : Optimisation et Simplification**

C++23 affine les améliorations de C++20 en introduisant std::expected pour une meilleure gestion des erreurs.


---

### **3.1. std::expected : Alternative à std::optional pour les Erreurs**

Alors que std::optional permet d'indiquer l'absence de valeur, std::expected permet de retourner soit une valeur valide, soit une erreur explicite.


---

#### **3.1.1. Problème avec std::optional**

Avec std::optional, nous ne savons pas pourquoi une fonction a échoué.

Exemple : Utilisation de std::optional
```cpp
#include <iostream>
#include <optional>

using namespace std;

optional<int> divide(int a, int b) {
    if (b == 0) return nullopt; // Erreur mais aucune indication du problème
    return a / b;
}
```
Problème :

nullopt indique une erreur, mais on ne sait pas laquelle !



---

#### **3.1.2. Solution avec std::expected**
Avec std::expected, on peut préciser la raison de l'échec.

Exemple : std::expected pour gérer les erreurs
```cpp
#include <iostream>
#include <expected>

using namespace std;

expected<int, string> safeDivide(int a, int b) {
    if (b == 0) return unexpected("Division par zéro");
    return a / b;
}

int main() {
    auto result = safeDivide(10, 0);
    
    if (result) { // Vérifie si la division a réussi
        cout << "Résultat : " << *result << endl;
    } else {
        cout << "Erreur : " << result.error() << endl; // Affiche le message d'erreur
    }

    return 0;
}
```
Explication du code :

1. Déclaration de std::expected<int, string> → Retourne soit un int, soit une erreur string.


2. Si b == 0, on retourne unexpected("Division par zéro") → Spécifie l’erreur.


3. Dans main(), on vérifie si result est valide → Affiche le résultat ou le message d’erreur.



> Avantage : std::expected permet une gestion plus précise des erreurs.




---

## **Conclusion**

C++17 → Introduit std::optional, std::variant, if constexpr.

C++20 → Concepts (concept), coroutines (co_await), et ranges (std::ranges).

C++23 → std::expected pour une meilleure gestion des erreurs.

