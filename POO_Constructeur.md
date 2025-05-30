# Relations entre Objets en UML - Guide JavaScript

## 1. L'Association

### Définition
L'association est la relation la plus basique entre deux classes. Elle indique qu'il existe un lien entre les objets de ces classes, mais sans notion de propriété ou de dépendance forte.

### Schéma de base
```
┌─────────────┐                    ┌─────────────┐
│   Classe A  │────────────────────│   Classe B  │
└─────────────┘                    └─────────────┘
```

### Types d'associations

#### Association Simple (Bidirectionnelle)
```
┌─────────────┐                    ┌─────────────┐
│  Étudiant   │◄──── étudie ─────▶│    Cours    │
├─────────────┤                    ├─────────────┤
│ - nom       │                    │ - nom       │
│ - âge       │                    │ - code      │
│ + étudier() │                    │ + enseigner()│
└─────────────┘                    └─────────────┘
```

#### Association Unidirectionnelle
```
┌─────────────┐                    ┌─────────────┐
│   Client    │──── commande ─────▶│  Produit    │
├─────────────┤                    ├─────────────┤
│ - nom       │                    │ - nom       │
│ - email     │                    │ - prix      │
│ + commander()│                   │ + getInfo() │
└─────────────┘                    └─────────────┘
```

### Multiplicités dans les associations
```
      1              *
┌─────────────┐◄────────────────▶┌─────────────┐
│  Personne   │     possède      │   Voiture   │
├─────────────┤                  ├─────────────┤
│ - nom       │                  │ - marque    │
│ - permis    │                  │ - modèle    │
└─────────────┘                  └─────────────┘

• Une personne peut posséder 0 à plusieurs voitures (*)
• Une voiture appartient à exactement 1 personne (1)
```

### Exemple concret en JavaScript
```javascript
class Étudiant {
    constructor(nom, âge) {
        this.nom = nom;
        this.âge = âge;
        this.coursInscrits = []; // Association
    }
    
    sInscrire(cours) {
        this.coursInscrits.push(cours);
        cours.ajouterÉtudiant(this); // Association bidirectionnelle
    }
    
    étudier() {
        console.log(`${this.nom} étudie ses ${this.coursInscrits.length} cours`);
    }
}

class Cours {
    constructor(nom, code) {
        this.nom = nom;
        this.code = code;
        this.étudiants = []; // Association inverse
    }
    
    ajouterÉtudiant(étudiant) {
        if (!this.étudiants.includes(étudiant)) {
            this.étudiants.push(étudiant);
        }
    }
    
    enseigner() {
        console.log(`Cours ${this.nom} enseigné à ${this.étudiants.length} étudiants`);
    }
}

// Utilisation
const étudiant1 = new Étudiant("Alice", 20);
const cours1 = new Cours("JavaScript", "JS101");

étudiant1.sInscrire(cours1); // Association créée
// Les deux objets existent indépendamment
```

## 2. L'Agrégation

### Définition
L'agrégation est une forme spécialisée d'association qui représente une relation "tout-partie" où les parties peuvent exister indépendamment du tout.

### Caractéristiques
- Relation "HAS-A" faible
- Les objets contenus peuvent survivre sans le conteneur
- Partage possible des parties entre plusieurs "touts"
- Cycle de vie indépendant

### Schéma UML
```
┌─────────────┐                    ┌─────────────┐
│    Tout     │◇──────────────────▶│   Partie    │
└─────────────┘                    └─────────────┘
      ▲                                   ▲
   Losange vide                    Peut exister seule
```

### Exemples détaillés

#### Exemple 1: Équipe et Joueurs
```
┌─────────────┐       1..*         ┌─────────────┐
│   Équipe    │◇──────────────────▶│   Joueur    │
├─────────────┤                    ├─────────────┤
│ - nom       │                    │ - nom       │
│ - ville     │                    │ - poste     │
│ - division  │                    │ - numéro    │
├─────────────┤                    ├─────────────┤
│ + jouer()   │                    │ + jouer()   │
│ + entrainer()│                   │ + marquer() │
└─────────────┘                    └─────────────┘

Pourquoi c'est une agrégation ?
• Si l'équipe est dissoute, les joueurs continuent d'exister
• Un joueur peut changer d'équipe
• Les joueurs ont une existence propre
```

#### Exemple 2: Playlist et Chansons
```
┌─────────────┐       0..*         ┌─────────────┐
│  Playlist   │◇──────────────────▶│   Chanson   │
├─────────────┤                    ├─────────────┤
│ - nom       │                    │ - titre     │
│ - auteur    │                    │ - artiste   │
│ - durée     │                    │ - durée     │
├─────────────┤                    ├─────────────┤
│ + jouer()   │                    │ + jouer()   │
│ + mélanger()│                    │ + pause()   │
└─────────────┘                    └─────────────┘
```

### Code JavaScript pour l'agrégation
```javascript
class Équipe {
    constructor(nom, ville) {
        this.nom = nom;
        this.ville = ville;
        this.joueurs = []; // Agrégation
    }
    
    ajouterJoueur(joueur) {
        // Le joueur existe déjà, on ne le crée pas
        if (!this.joueurs.includes(joueur)) {
            this.joueurs.push(joueur);
            joueur.rejoindreÉquipe(this);
        }
    }
    
    retirerJoueur(joueur) {
        const index = this.joueurs.indexOf(joueur);
        if (index > -1) {
            this.joueurs.splice(index, 1);
            joueur.quitterÉquipe();
            // Le joueur continue d'exister après retrait
        }
    }
    
    jouer() {
        console.log(`L'équipe ${this.nom} joue avec ${this.joueurs.length} joueurs`);
        this.joueurs.forEach(joueur => joueur.jouer());
    }
    
    // Si l'équipe est détruite, les joueurs survivent
    détruire() {
        this.joueurs.forEach(joueur => joueur.quitterÉquipe());
        this.joueurs = [];
        console.log(`Équipe ${this.nom} dissoute, mais les joueurs existent toujours`);
    }
}

class Joueur {
    constructor(nom, numéro, poste) {
        this.nom = nom;
        this.numéro = numéro;
        this.poste = poste;
        this.équipe = null;
    }
    
    rejoindreÉquipe(équipe) {
        this.équipe = équipe;
    }
    
    quitterÉquipe() {
        this.équipe = null;
    }
    
    jouer() {
        console.log(`${this.nom} (n°${this.numéro}) joue en ${this.poste}`);
    }
}

// Exemple d'utilisation
const équipe = new Équipe("Les Lions", "Paris");
const joueur1 = new Joueur("Marc", 10, "Attaquant");
const joueur2 = new Joueur("Paul", 5, "Défenseur");

// Les joueurs existent indépendamment
équipe.ajouterJoueur(joueur1);
équipe.ajouterJoueur(joueur2);

équipe.jouer();

// Si l'équipe disparaît, les joueurs continuent d'exister
équipe.détruire();
console.log(`${joueur1.nom} existe toujours !`); // Marc existe toujours !
```

## 3. La Composition

### Définition
La composition est la forme la plus forte de relation "tout-partie" où les parties ne peuvent pas exister sans le tout. C'est une relation de propriété exclusive.

### Caractéristiques
- Relation "HAS-A" forte
- Cycle de vie lié : si le tout meurt, les parties meurent
- Propriété exclusive : une partie appartient à un seul tout
- Responsabilité de création/destruction

### Schéma UML
```
┌─────────────┐                    ┌─────────────┐
│    Tout     │♦──────────────────▶│   Partie    │
└─────────────┘                    └─────────────┘
      ▲                                   ▲
   Losange plein                 Dépendante du tout
```

### Exemples détaillés

#### Exemple 1: Voiture et Moteur
```
┌─────────────┐       1..1         ┌─────────────┐
│   Voiture   │♦──────────────────▶│   Moteur    │
├─────────────┤                    ├─────────────┤
│ - marque    │                    │ - cylindrée │
│ - modèle    │                    │ - puissance │
│ - année     │                    │ - type      │
├─────────────┤                    ├─────────────┤
│ + démarrer()│                    │ + tourner() │
│ + rouler()  │                    │ + arrêter() │
└─────────────┘                    └─────────────┘
```

#### Exemple 2: Site Web et Pages
```
┌─────────────┐       1..*         ┌─────────────┐
│  SiteWeb    │♦──────────────────▶│    Page     │
├─────────────┤                    ├─────────────┤
│ - nom       │                    │ - titre     │
│ - domaine   │                    │ - contenu   │
│ - thème     │                    │ - url       │
├─────────────┤                    ├─────────────┤
│ + publier() │                    │ + afficher()│
│ + supprimer│                     │ + modifier()│
└─────────────┘                    └─────────────┘
```

### Code JavaScript pour la composition
```javascript
class Voiture {
    constructor(marque, modèle, année) {
        this.marque = marque;
        this.modèle = modèle;
        this.année = année;
        
        // Composition : la voiture crée son moteur
        this.moteur = new Moteur(2000, 150, "Essence", this);
        this.roues = [
            new Roue("Avant Gauche", this),
            new Roue("Avant Droite", this),
            new Roue("Arrière Gauche", this),
            new Roue("Arrière Droite", this)
        ];
        
        console.log(`Voiture ${marque} ${modèle} créée avec moteur et roues`);
    }
    
    démarrer() {
        console.log(`${this.marque} ${this.modèle} démarre`);
        this.moteur.tourner();
    }
    
    rouler() {
        console.log(`${this.marque} ${this.modèle} roule`);
        this.roues.forEach(roue => roue.tourner());
    }
    
    // Destruction en cascade (composition)
    détruire() {
        console.log(`Destruction de la voiture ${this.marque} ${this.modèle}`);
        
        // Le moteur et les roues sont détruits avec la voiture
        this.moteur.détruire();
        this.roues.forEach(roue => roue.détruire());
        
        this.moteur = null;
        this.roues = [];
        
        console.log("Voiture et tous ses composants détruits");
    }
}

class Moteur {
    constructor(cylindrée, puissance, type, voiture) {
        this.cylindrée = cylindrée;
        this.puissance = puissance;
        this.type = type;
        this.voiture = voiture; // Référence vers le propriétaire
        this.enMarche = false;
    }
    
    tourner() {
        this.enMarche = true;
        console.log(`Moteur ${this.type} de ${this.puissance}ch en marche`);
    }
    
    arrêter() {
        this.enMarche = false;
        console.log("Moteur arrêté");
    }
    
    // Le moteur ne peut pas survivre sans sa voiture
    détruire() {
        this.arrêter();
        console.log("Moteur détruit avec la voiture");
        this.voiture = null;
    }
}

class Roue {
    constructor(position, voiture) {
        this.position = position;
        this.voiture = voiture;
        this.enRotation = false;
    }
    
    tourner() {
        this.enRotation = true;
        console.log(`Roue ${this.position} tourne`);
    }
    
    détruire() {
        this.enRotation = false;
        console.log(`Roue ${this.position} détruite`);
        this.voiture = null;
    }
}

// Exemple d'utilisation
const maVoiture = new Voiture("Renault", "Clio", 2023);

maVoiture.démarrer();
maVoiture.rouler();

// Tentative d'utiliser le moteur indépendamment
// const moteurSeul = maVoiture.moteur; // Ceci ne devrait pas être fait !

// Destruction : tout disparaît ensemble
maVoiture.détruire();
// Le moteur et les roues n'existent plus
```

### Exemple avec une classe Document
```javascript
class Document {
    constructor(titre, auteur) {
        this.titre = titre;
        this.auteur = auteur;
        this.pages = []; // Composition
        this.créé = new Date();
        
        // Création automatique de pages (composition)
        this.ajouterPage("Page de garde");
        this.ajouterPage("Table des matières");
    }
    
    ajouterPage(contenu) {
        // Le document crée ses pages
        const nouvellePage = new Page(this.pages.length + 1, contenu, this);
        this.pages.push(nouvellePage);
        return nouvellePage;
    }
    
    supprimerPage(numéro) {
        const page = this.pages.find(p => p.numéro === numéro);
        if (page) {
            page.détruire();
            this.pages = this.pages.filter(p => p.numéro !== numéro);
        }
    }
    
    imprimer() {
        console.log(`\n=== Document: ${this.titre} ===`);
        this.pages.forEach(page => page.afficher());
    }
    
    // Destruction en cascade
    détruire() {
        console.log(`Destruction du document "${this.titre}"`);
        this.pages.forEach(page => page.détruire());
        this.pages = [];
    }
}

class Page {
    constructor(numéro, contenu, document) {
        this.numéro = numéro;
        this.contenu = contenu;
        this.document = document; // Une page appartient à UN document
    }
    
    modifier(nouveauContenu) {
        this.contenu = nouveauContenu;
        console.log(`Page ${this.numéro} modifiée`);
    }
    
    afficher() {
        console.log(`Page ${this.numéro}: ${this.contenu}`);
    }
    
    // Une page ne peut pas exister sans son document
    détruire() {
        console.log(`Page ${this.numéro} détruite`);
        this.document = null;
    }
}

// Utilisation
const monDoc = new Document("Guide JavaScript", "Alice");
monDoc.ajouterPage("Introduction à JavaScript");
monDoc.ajouterPage("Les classes ES6");

monDoc.imprimer();

// Si le document est détruit, toutes ses pages disparaissent
monDoc.détruire();
```

## Tableau Comparatif JavaScript

| **Critère** | **Association** | **Agrégation** | **Composition** |
|-------------|----------------|----------------|-----------------|
| **Symbole UML** | `────` | `◇────` | `♦────` |
| **Relation** | "utilise/connaît" | "a des" (faible) | "possède" (forte) |
| **Création** | Objets créés séparément | Objets créés séparément | Parent crée les enfants |
| **Destruction** | Indépendante | Indépendante | En cascade |
| **Exemple JS** | `étudiant.cours = []` | `équipe.joueurs = []` | `voiture.moteur = new Moteur()` |
| **Cycle de vie** | Indépendant | Indépendant | Lié au parent |

## Schéma Récapitulatif JavaScript

```
ÉVOLUTION DE LA FORCE DE LA RELATION EN JAVASCRIPT
         
Association    ──▶    Agrégation    ──▶    Composition
    │                      │                    │
    ▼                      ▼                    ▼
┌─────────────┐        ┌─────────────┐      ┌─────────────┐
│ obj1.ref =  │        │ parent.list │      │ parent.part │
│ existingObj │        │ .push(obj)  │      │ = new Part()│
│             │        │             │      │             │
│ Référence   │        │ Collection  │      │ Création    │
│ externe     │        │ d'existants │      │ interne     │
└─────────────┘        └─────────────┘      └─────────────┘

étudiant.cours    équipe.joueurs     voiture.moteur
client.commandes  playlist.chansons  document.pages
```

## Conseils pour JavaScript

1. **Association** : Utilisez des références vers des objets existants
2. **Agrégation** : Utilisez des arrays pour collecter des objets indépendants
3. **Composition** : Créez les objets enfants dans le constructeur du parent
4. **Destruction** : Implémentez des méthodes `détruire()` pour la composition