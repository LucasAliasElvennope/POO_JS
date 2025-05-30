# POO : Comprendre le Principe d'Encapsulation

L'**encapsulation** est l'un des quatre piliers fondamentaux de la Programmation Orientée Objet (POO), avec l'abstraction, l'héritage et le polymorphisme. Elle consiste à **regrouper les données (attributs) et les méthodes** qui opèrent sur ces données au sein d'une seule unité (l'objet), tout en **cachant les détails d'implémentation internes** de l'objet.

---

### Qu'est-ce que l'encapsulation ?

L'encapsulation peut être imaginée comme une **boîte noire**. L'objet est cette boîte noire :
* À l'intérieur de la boîte, il y a des **données (attributs)** et des **mécanismes (méthodes)** complexes qui interagissent entre eux.

* De l'extérieur, vous n'avez accès qu'à une **interface publique** (certaines méthodes) qui vous permet d'interagir avec la boîte, sans savoir précisément comment elle fonctionne à l'intérieur.

En d'autres termes, l'encapsulation signifie que l'**état interne d'un objet est protégé** et ne peut être modifié que par les méthodes de l'objet lui-même. Vous ne pouvez pas directement "toucher" les données internes de l'objet ; vous devez passer par les "portes" (les méthodes) que l'objet expose.

---

### À quoi sert l'encapsulation ?

L'encapsulation offre de nombreux avantages cruciaux :

1.  **Protection des données (Sécurité) :** Elle empêche l'accès direct et la modification non intentionnelle ou non valide des données internes de l'objet. L'objet a un contrôle total sur la manière dont ses propres données sont manipulées.

2.  **Modularité :** Elle rend le code plus modulaire en isola
nt les changements. Si vous modifiez l'implémentation interne d'une méthode ou la structure interne des données, cela n'affectera pas le code externe qui utilise cette méthode, tant que l'interface publique (la signature de la méthode) reste la même.

3.  **Maintenance et Évolution :** Facilite la maintenance et la mise à jour du code. Les modifications sont localisées à l'intérieur de l'objet. Vous pouvez refactoriser ou améliorer l'implémentation interne sans casser le code client.

4.  **Cohérence de l'état :** En forçant l'utilisation de méthodes spécifiques pour interagir avec ses données, l'encapsulation assure que l'objet reste toujours dans un état cohérent et valide. Par exemple, un setter peut valider une nouvelle valeur avant de l'assigner à un attribut.

5.  **Simplicité d'utilisation :** Elle masque la complexité interne, présentant une interface simple et intuitive à l'utilisateur de l'objet.

---

### Quels sont les différents mots-clés réservés pour l'encapsulation ?

Les mots-clés utilisés pour contrôler la visibilité des membres d'une classe varient selon les langages de programmation. Les plus courants sont les **modificateurs d'accès** :

* **`public` :** Les membres (attributs et méthodes) déclarés comme `public` sont **accessibles depuis n'importe où** dans le programme, à l'intérieur ou à l'extérieur de la classe. C'est l'interface de l'objet.
* **`private` :** Les membres déclarés comme `private` ne sont **accessibles qu'à l'intérieur de la classe** où ils sont définis. C'est le niveau d'encapsulation le plus restrictif, et il est utilisé pour cacher les détails d'implémentation.
* **`protected` :** Les membres déclarés comme `protected` sont accessibles à l'intérieur de la classe où ils sont définis et par les **classes qui en héritent** (les sous-classes). Ils ne sont généralement pas accessibles de l'extérieur par des objets ordinaires.

**Note sur JavaScript :**
Historiquement, JavaScript n'avait pas de modificateurs d'accès `public`, `private`, `protected` au sens strict des langages comme Java ou C#. La convention pour les membres "privés" était de les préfixer par un underscore (`_`) (par exemple, `this._maVariablePrivée`). Cependant, depuis **ES2020 (ECMAScript 2020)**, les **champs de classe privés** sont devenus une fonctionnalité standard, utilisant le préfixe `#` (par exemple, `#monAttributPrivé`).

```javascript
class CompteBancaire {
  #solde; // Champ de classe privé en JavaScript

  constructor(soldeInitial) {
    this.#solde = soldeInitial;
  }

  deposer(montant) {
    if (montant > 0) {
      this.#solde += montant;
      console.log(`Dépôt de ${montant}€. Nouveau solde : ${this.#solde}€`);
    } else {
      console.error("Le montant du dépôt doit être positif.");
    }
  }

  retirer(montant) {
    if (montant > 0 && montant <= this.#solde) {
      this.#solde -= montant;
      console.log(`Retrait de ${montant}€. Nouveau solde : ${this.#solde}€`);
    } else {
      console.error("Montant de retrait invalide ou solde insuffisant.");
    }
  }

  getSolde() { // Méthode publique pour accéder au solde
    return this.#solde;
  }
}

const monCompte = new CompteBancaire(100);
monCompte.deposer(50);     // OK
// console.log(monCompte.#solde); // Erreur ! Impossible d'accéder directement au champ privé
console.log(monCompte.getSolde()); // OK, affiche 150