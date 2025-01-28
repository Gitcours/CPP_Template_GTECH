Voici la correction complète des exercices avec explications.  

---

## **Correction de l'Exercice 1 : Gestion des erreurs avec un code de retour**  

```cpp
#include <iostream>

int lireEntier(int& valeur) {
    std::cin >> valeur;
    
    if (std::cin.fail()) {
        std::cin.clear();  // Réinitialise le flux en cas d'erreur
        std::string invalidInput;
        std::cin >> invalidInput;  // Capture l'entrée invalide pour la "vider"
        return -1;
    }
    
    return 0;
}

int main() {
    int nombre;
    std::cout << "Entrez un entier : ";
    
    if (lireEntier(nombre) != 0) {
        std::cerr << "Erreur : entrée invalide !" << std::endl;
    } else {
        std::cout << "Vous avez saisi : " << nombre << std::endl;
    }
    
    return 0;
}

```
### **Explication :**  
- `std::cin.fail()` vérifie si l’entrée utilisateur est invalide.  
- `std::cin.clear()` réinitialise le flux d’entrée en cas d’erreur.  
- `std::cin.ignore(...)` ignore les caractères invalides jusqu'à la fin de la ligne.  

---

## **Correction de l'Exercice 2 : Gestion des erreurs avec `errno` et `perror`**  

```cpp
#include <iostream>
#include <cstdio>
#include <cerrno>

int main() {
    FILE* file = fopen("data.txt", "r");
    if (!file) {
        perror("Erreur d'ouverture du fichier");
        return -1;
    }

    std::cout << "Fichier ouvert avec succès" << std::endl;
    fclose(file);
    return 0;
}
```
### **Explication :**  
- `fopen("data.txt", "r")` tente d’ouvrir le fichier en lecture.  
- `perror("Erreur")` affiche un message détaillé basé sur `errno`.  
- `fclose(file)` ferme le fichier s’il a été ouvert avec succès.  

---

## **Correction de l'Exercice 3 : Gestion des erreurs avec les exceptions**  

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
        std::cerr << "Exception capturée : " << e.what() << std::endl;
    }
    return 0;
}
```
### **Explication :**  
- `throw std::runtime_error("message")` lève une exception en cas de division par zéro.  
- `catch (const std::exception& e)` attrape l’exception et affiche le message d’erreur.  

---

## **Correction de l'Exercice 4 : Gestion des erreurs avec `std::invalid_argument`**  

```cpp
#include <iostream>
#include <stdexcept>

void inscrireUtilisateur(int age) {
    if (age < 18) {
        throw std::invalid_argument("âge non valide !");
    }
}

int main() {
    int age;
    std::cout << "Entrez votre âge : ";
    std::cin >> age;

    try {
        inscrireUtilisateur(age);
        std::cout << "Inscription réussie !" << std::endl;
    } catch (const std::invalid_argument& e) {
        std::cerr << "Erreur : " << e.what() << std::endl;
    }
    return 0;
}
```
### **Explication :**  
- `throw std::invalid_argument("message")` lève une exception si l’âge est < 18.  
- `catch (const std::invalid_argument& e)` attrape et affiche le message d’erreur.  

---

## **Correction de l'Exercice 5 : Gestion des erreurs avancée**  

```cpp
#include <iostream>
#include <stdexcept>
#include <limits>

int lireEntier(int& valeur) {
    std::cin >> valeur;
    if (std::cin.fail()) {
        std::cin.clear();
        std::cin.ignore(std::numeric_limits<std::streamsize>::max(), '\n');
        return -1;
    }
    return 0;
}

int division(int a, int b) {
    if (b == 0) {
        throw std::runtime_error("Division par zéro !");
    }
    return a / b;
}

int main() {
    int a, b;

    std::cout << "Entrez un nombre : ";
    while (lireEntier(a) != 0) {
        std::cerr << "Erreur : entrée invalide !" << std::endl;
        std::cout << "Entrez un nombre : ";
    }

    std::cout << "Entrez un diviseur : ";
    while (true) {
        if (lireEntier(b) == 0) {
            try {
                std::cout << "Résultat : " << division(a, b) << std::endl;
                break;
            } catch (const std::exception& e) {
                std::cerr << "Erreur : " << e.what() << std::endl;
            }
        } else {
            std::cerr << "Erreur : entrée invalide !" << std::endl;
        }
        std::cout << "Entrez un diviseur : ";
    }

    return 0;
}
```
### **Explication :**  
- `lireEntier()` vérifie que l’utilisateur entre bien un entier.  
- `division()` lève une exception si `b == 0`.  
- `while (true)` permet de redemander une entrée valide si nécessaire.  

---

## **Conclusion**  

Ces corrections permettent de **gérer correctement les erreurs** en utilisant **codes de retour, errno/perror et exceptions**. Elles offrent une **gestion robuste et interactive** des erreurs, tout en garantissant une **bonne expérience utilisateur**.  

Tu peux maintenant tester ces solutions et les adapter à tes besoins !
