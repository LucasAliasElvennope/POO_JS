# POO : Différences entre Classes Concrètes et Abstraites

En Programmation Orientée Objet (POO), les classes peuvent être classées en deux catégories principales : **concrètes** et **abstraites**.

---

### Classes Concrètes

Une **classe concrète** est une classe "normale" ou "standard" dont vous pouvez directement créer des **instances** (des objets).
* Elle est **entièrement implémentée**, ce qui signifie que toutes ses méthodes (fonctions) ont un corps de code qui décrit leur comportement.
* Vous pouvez utiliser le mot-clé `new` (dans la plupart des langages) pour créer un objet à partir d'une classe concrète.

**Exemple conceptuel :**
Si vous avez une classe `Chien` qui a des attributs comme `nom` et `race`, et des méthodes comme `aboyer()` et `manger()`, et que toutes ces méthodes ont une implémentation complète, alors `Chien` est une classe concrète. Vous pouvez créer `monChien = new Chien("Rex", "Berger Allemand");`.

---

### Classes Abstraites

Une **classe abstraite** est une classe spéciale qui **ne peut pas être instanciée directement**. Son objectif principal est de servir de **modèle de base** pour d'autres classes (appelées "sous-classes" ou "classes dérivées").
* Elle peut contenir des **méthodes concrètes** (avec implémentation) et/ou des **méthodes abstraites** (déclarées avec le mot-clé `abstract` et **sans implémentation**).
* Les classes qui héritent d'une classe abstraite **doivent fournir une implémentation** pour toutes les méthodes abstraites de la classe parente. Si une sous-classe ne le fait pas, elle doit elle-même être déclarée comme abstraite.
* Elles sont utilisées pour définir une **interface commune** ou un **comportement de base** que les sous-classes doivent suivre, forçant ainsi une certaine structure.

**Exemple conceptuel :**
Imaginons une classe `Animal` qui a une méthode `faireSon()` mais dont le comportement exact de `faireSon()` dépend de l'animal spécifique. Vous pourriez déclarer `Animal` comme une classe abstraite avec une méthode `faireSon()` abstraite.
* La classe `Animal` ne peut pas être instanciée (vous ne pouvez pas créer un `nouvel Animal` générique).
* Les sous-classes comme `Chien` et `Chat` hériteraient d' `Animal` et devraient implémenter leur propre version de `faireSon()` (par exemple, `aboyer()` pour `Chien`, `miauler()` pour `Chat`).

---

**En résumé :**
| Caractéristique    | Classe Concrète                               | Classe Abstraite                                       |
| :----------------- | :-------------------------------------------- | :----------------------------------------------------- |
| **Instanciation** | Peut être instanciée (créer des objets)       | Ne peut **PAS** être instanciée directement             |
| **Implémentation** | Toutes les méthodes sont implémentées         | Peut contenir des méthodes abstraites (sans impl.)    |
| **Héritage** | Peut être héritée par d'autres classes        | Doit être héritée ; les sous-classes implémentent les méthodes abstraites |
| **But** | Représenter une entité spécifique             | Fournir un modèle ou une base pour les sous-classes   |

---

**Lien utile :**
* [GeeksforGeeks - Difference between Abstract Class and Concrete Class](https://www.geeksforgeeks.org/difference-between-abstract-class-and-concrete-class-in-java/) (L'exemple est en Java, mais le concept est universel en POO)