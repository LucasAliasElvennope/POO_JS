# Qu'est-ce que l'Abstraction (très simplement) ?

L'abstraction, c'est comme utiliser un bouton sur une machine. Vous appuyez sur le bouton, et la machine fait quelque chose. Vous n'avez pas besoin de savoir ce qui se passe à l'intérieur de la machine pour que ça marche.

En programmation, on abstrait quand on crée une fonction ou une méthode qui fait quelque chose de complexe, mais que l'utilisateur n'a besoin de connaître que le nom de cette fonction et ce qu'elle fait (son rôle), pas comment elle le fait (ses détails internes).

**Exemple Simple en JavaScript : Une Lampe**

Imaginons que nous voulons contrôler une lampe.
Sans Abstraction (approche directe)

**On pourrait faire quelque chose comme ça :**

```javascript
let etatAmpoule = "éteinte"; // Variable pour l'état de l'ampoule

function allumerAmpoule() {
  // Détails spécifiques pour allumer une ampoule
  console.log("J'envoie du courant à l'ampoule.");
  console.log("Le filament de l'ampoule chauffe.");
  etatAmpoule = "allumée";
  console.log("L'ampoule est maintenant " + etatAmpoule + ".");
}

function eteindreAmpoule() {
  // Détails spécifiques pour éteindre une ampoule
  console.log("Je coupe le courant de l'ampoule.");
  etatAmpoule = "éteinte";
  console.log("L'ampoule est maintenant " + etatAmpoule + ".");
}

allumerAmpoule();
// La personne qui utilise ces fonctions doit savoir comment appeler allumerAmpoule() et eteindreAmpoule().
```

Ici, les fonctions sont directement liées à l'idée d'une ampoule et montrent ce qui se passe "à l'intérieur".

**Avec Abstraction (approche POO)**

Maintenant, pensons "Lampe" comme un objet qui offre une interface simple : un bouton "allumer" et un bouton "éteindre". Peu importe si elle a une ampoule, des LED, ou autre chose à l'intérieur.

```javascript
class Lampe {
  constructor() {
    this.estAllumee = false; // C'est l'état interne de la lampe
  }

  // C'est la méthode "abstraite" (le bouton)
  // L'utilisateur appelle juste allumer(), sans savoir comment
  allumer() {
    if (!this.estAllumee) {
      console.log("Clic !"); // Ce qui se passe quand on appuie sur le bouton
      // Ici, on pourrait avoir des détails complexes :
      // - Vérifier la tension
      // - Envoyer un signal à des LED
      // - Etc.
      // MAIS on les CACHE à l'utilisateur de la méthode.
      this.estAllumee = true;
      console.log("La lampe est allumée.");
    } else {
      console.log("La lampe est déjà allumée.");
    }
  }

  // Autre méthode abstraite (bouton)
  eteindre() {
    if (this.estAllumee) {
      console.log("Clac !"); // Ce qui se passe quand on appuie sur le bouton
      this.estAllumee = false;
      console.log("La lampe est éteinte.");
    } else {
      console.log("La lampe est déjà éteinte.");
    }
  }

  // Une méthode pour voir l'état sans accéder directement à 'estAllumee'
  afficherEtat() {
    console.log(`La lampe est actuellement : ${this.estAllumee ? 'ON' : 'OFF'}`);
  }
}

// Utilisation de la lampe :
const maLampe = new Lampe(); // Je crée ma lampe (un objet)

maLampe.allumer();    // J'appuie sur le bouton "allumer"
maLampe.afficherEtat();

maLampe.eteindre();   // J'appuie sur le bouton "éteindre"
maLampe.afficherEtat();

maLampe.allumer(); // J'appuie à nouveau
```

**Ce que l'Abstraction Fait ici :**

- Bouton Simple : L'utilisateur de la Lampe n'a qu'à savoir qu'il existe des méthodes allumer() et eteindre(). C'est l'interface simple que l'objet Lampe lui offre.

- Détails Cachés : Les messages "J'envoie du courant...", "Le filament chauffe..." ont disparu de l'interface publique. Ils sont abstraits (cachés) à l'intérieur de la méthode allumer() et eteindre(). La complexité sous-jacente est masquée.

- Flexibilité : Si un jour, notre lampe utilise des LED au lieu d'une ampoule à filament, nous n'aurions qu'à modifier l'intérieur de la méthode allumer() dans la classe Lampe. Le code qui utilise maLampe.allumer() n'aurait absolument pas besoin de changer.

En somme, l'abstraction, c'est comme simplifier une boîte complexe en y mettant juste quelques boutons visibles pour interagir avec elle, sans avoir à s'inquiéter de tous les fils et circuits à l'intérieur.