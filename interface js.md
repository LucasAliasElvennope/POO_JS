# Les Interfaces en JavaScript

## Qu'est-ce qu'une interface ?

En JavaScript, une **interface** n'existe pas nativement comme dans d'autres langages. Cependant, le concept existe : c'est un "contrat" qui définit quelles méthodes un objet doit avoir, sans spécifier comment elles fonctionnent.

### Schéma conceptuel :

```
"Interface" Volant (concept)
├── méthode: voler() (doit exister)
├── méthode: atterrir() (doit exister)
└── méthode: altitude() (doit exister)
```

### Schéma de flux - Comment ça marche :

```
    [Interface Volant]
           │
           │ définit le contrat
           ▼
    ┌─────────────────┐
    │ Règles à suivre │
    │ • voler()       │
    │ • atterrir()    │  
    │ • altitude()    │
    └─────────────────┘
           │
           │ respectent le contrat
           ▼
    ┌──────┬──────┬──────┐
    │Avion │Oiseau│Drone │
    │  ✓   │  ✓   │  ✓   │
    └──────┴──────┴──────┘
```

### Exemple en JavaScript - Simulation d'interface :

```javascript
// On simule une "interface" avec une documentation et des vérifications
function verifierInterfaceVolant(objet) {
    const methodesRequises = ['voler', 'atterrir', 'altitude'];
    
    for (let methode of methodesRequises) {
        if (typeof objet[methode] !== 'function') {
            throw new Error(`L'objet doit avoir la méthode ${methode}()`);
        }
    }
    return true;
}

// Classes qui "implémentent" notre interface conceptuelle
class Avion {
    constructor() {
        this.altitudeActuelle = 0;
    }
    
    voler() {
        console.log("L'avion décolle avec ses réacteurs");
        this.altitudeActuelle = 10000;
    }
    
    atterrir() {
        console.log("L'avion atterrit sur la piste");
        this.altitudeActuelle = 0;
    }
    
    altitude() {
        return this.altitudeActuelle;
    }
}

class Oiseau {
    constructor() {
        this.altitudeActuelle = 0;
    }
    
    voler() {
        console.log("L'oiseau bat des ailes");
        this.altitudeActuelle = 500;
    }
    
    atterrir() {
        console.log("L'oiseau se pose sur une branche");
        this.altitudeActuelle = 0;
    }
    
    altitude() {
        return this.altitudeActuelle;
    }
}

// Utilisation
const avion = new Avion();
const oiseau = new Oiseau();

verifierInterfaceVolant(avion);  // ✅ OK
verifierInterfaceVolant(oiseau); // ✅ OK
```

## Intérêt des interfaces (concept)

### 1. **Polymorphisme**

#### Schéma du polymorphisme :
```
    gererVol(objet)
         │
         ▼
    [Vérification]
    objet.voler exists?
    objet.atterrir exists?
    objet.altitude exists?
         │
         ▼ Si OUI
    ┌─────────────────┐
    │ objet.voler()   │
    │ objet.altitude()│
    │ objet.atterrir()│
    └─────────────────┘
         │
         ▼
    Fonctionne avec :
    • Avion ✓
    • Oiseau ✓  
    • Drone ✓
    • N'importe quel objet "volant" ✓
```

Traiter différents objets de la même manière :

```javascript
function gererVol(objet) {
    // Vérification optionnelle du "contrat"
    verifierInterfaceVolant(objet);
    
    objet.voler();
    console.log(`Altitude: ${objet.altitude()}m`);
    objet.atterrir();
}

// Fonctionne avec n'importe quel objet ayant les bonnes méthodes
gererVol(new Avion());    // ✅
gererVol(new Oiseau());   // ✅
```

### 2. **Approche avec des "Mixins" (plus JavaScript)**

#### Schéma du pattern Mixin :
```
    InterfaceVolant (Mixin)
    ┌──────────────────────┐
    │ voler() { throw }    │
    │ atterrir() { throw } │  
    │ altitude() { throw } │
    └──────────────────────┘
             │
             │ Object.assign()
             ▼
    ┌──────────────────────┐
    │ Helicoptere          │
    │ ├── voler() ✓        │ ← redéfinies
    │ ├── atterrir() ✓     │ ← redéfinies  
    │ └── altitude() ✓     │ ← redéfinies
    └──────────────────────┘
```

```javascript
// Mixin pour simuler une interface
const InterfaceVolant = {
    // Méthodes par défaut qui lancent des erreurs
    voler() {
        throw new Error("La méthode voler() doit être implémentée");
    },
    
    atterrir() {
        throw new Error("La méthode atterrir() doit être implémentée");
    },
    
    altitude() {
        throw new Error("La méthode altitude() doit être implémentée");
    }
};

// Classe qui utilise le mixin
class Helicoptere {
    constructor() {
        this.altitudeActuelle = 0;
        // Copier les méthodes de l'interface
        Object.assign(this, InterfaceVolant);
    }
    
    // On DOIT redéfinir ces méthodes sinon erreur
    voler() {
        console.log("L'hélicoptère monte verticalement");
        this.altitudeActuelle = 3000;
    }
    
    atterrir() {
        console.log("L'hélicoptère se pose en douceur");
        this.altitudeActuelle = 0;
    }
    
    altitude() {
        return this.altitudeActuelle;
    }
}

const helicoptere = new Helicoptere();
gererVol(helicoptere); // ✅ Fonctionne
```

### 3. **Duck Typing** (approche JavaScript naturelle)

#### Schéma du Duck Typing :
```
    "Si ça marche comme un canard..."
    
    peutVoler(objet) ?
    ┌─────────────────────┐
    │ objet.voler ?       │ ──► typeof function ✓
    │ objet.atterrir ?    │ ──► typeof function ✓  
    │ objet.altitude ?    │ ──► typeof function ✓
    └─────────────────────┘
             │ Toutes ✓
             ▼
    "...alors c'est un canard !"
    
    Peut accepter :
    • Classes (Avion, Oiseau)
    • Objets littéraux  
    • Fonctions constructeurs
    • N'importe quoi avec les bonnes méthodes !
```

```javascript
// En JavaScript, on utilise souvent le "Duck Typing"
// "Si ça marche comme un canard, c'est un canard"

function peutVoler(objet) {
    return typeof objet.voler === 'function' && 
           typeof objet.atterrir === 'function' && 
           typeof objet.altitude === 'function';
}

function gererVolDuckTyping(objet) {
    if (!peutVoler(objet)) {
        throw new Error("L'objet ne peut pas voler (méthodes manquantes)");
    }
    
    objet.voler();
    console.log(`Altitude: ${objet.altitude()}m`);
    objet.atterrir();
}

// Même un objet littéral peut "voler" s'il a les bonnes méthodes
const droneMaison = {
    altitudeActuelle: 0,
    
    voler() {
        console.log("Le drone décolle");
        this.altitudeActuelle = 100;
    },
    
    atterrir() {
        console.log("Le drone atterrit");
        this.altitudeActuelle = 0;
    },
    
    altitude() {
        return this.altitudeActuelle;
    }
};

gererVolDuckTyping(droneMaison); // ✅ Fonctionne parfaitement !
```

## Pourquoi parler de classes abstraites ?

En JavaScript moderne, on peut simuler des classes abstraites :

### Exemple de classe "abstraite" en JavaScript :

#### Schéma de classe abstraite :
```
    Animal (classe abstraite)
    ┌─────────────────────────────┐
    │ constructor() {             │
    │   if (new.target === Animal)│ ← Empêche instanciation
    │     throw Error             │
    │ }                           │
    │                             │
    │ manger() { code concret }   │ ← Méthode avec implémentation
    │ dormir() { throw Error }    │ ← Méthode "abstraite"
    │ bouger() { throw Error }    │ ← Méthode "abstraite"  
    └─────────────────────────────┘
              │
              │ extends
              ▼
    ┌─────────────────────────────┐
    │ Chien extends Animal        │
    │                             │
    │ constructor(nom) {          │
    │   super(nom) ✓              │
    │ }                           │
    │                             │
    │ manger() héritée ✓          │
    │ dormir() { implémentée } ✓  │ ← DOIT être redéfinie
    │ bouger() { implémentée } ✓  │ ← DOIT être redéfinie
    └─────────────────────────────┘

    new Animal() ❌ → Error!
    new Chien()  ✓ → OK!
```

```javascript
class Animal {
    constructor(nom) {
        // Empêcher l'instanciation directe
        if (new.target === Animal) {
            throw new Error("Animal est une classe abstraite, ne peut pas être instanciée");
        }
        this.nom = nom;
    }
    
    // Méthode concrète (avec implémentation)
    manger() {
        console.log(`${this.nom} mange`);
    }
    
    // Méthode "abstraite" (doit être redéfinie)
    dormir() {
        throw new Error("La méthode dormir() doit être implémentée dans la classe fille");
    }
    
    bouger() {
        throw new Error("La méthode bouger() doit être implémentée dans la classe fille");
    }
}

class Chien extends Animal {
    constructor(nom) {
        super(nom);
    }
    
    // DOIT implémenter ces méthodes
    dormir() {
        console.log(`${this.nom} dort dans sa niche`);
    }
    
    bouger() {
        console.log(`${this.nom} court`);
    }
}

// Test
// const animal = new Animal("Test"); // ❌ Erreur !
const chien = new Chien("Rex");       // ✅ OK
chien.manger(); // Hérité d'Animal
chien.dormir(); // Implémenté dans Chien
```

## Différences conceptuelles

### Schéma comparatif détaillé :

```
INTERFACE (concept)                    CLASSE ABSTRAITE (simulation)
┌─────────────────────┐               ┌─────────────────────────┐
│ Volant              │               │ Animal                  │
├─────────────────────┤               ├─────────────────────────┤
│ Contrat pur :       │               │ Mélange :               │
│                     │               │                         │
│ voler() (requis)    │               │ + nom: string (concret) │
│ atterrir() (requis) │               │ + manger() { code }     │ ← Implémenté
│ altitude() (requis) │               │ + dormir() { erreur }   │ ← À implémenter  
│                     │               │ + bouger() { erreur }   │ ← À implémenter
│ Aucune propriété    │               │                         │
│ Aucune implémentation│               │ Constructeur possible   │
└─────────────────────┘               └─────────────────────────┘
         ▲                                       ▲
         │                                       │
   "implémente"                              extends
   (simulation)                            (héritage réel)
         │                                       │
┌─────────────────────┐               ┌─────────────────────────┐
│ Avion               │               │ Chien                   │
│                     │               │                         │
│ ✓ Toutes méthodes   │               │ ✓ Hérite nom, manger()  │
│   créées from zero  │               │ ✓ Redéfinit dormir()    │
│ ✓ Aucun héritage    │               │ ✓ Redéfinit bouger()    │
│ ✓ Liberté totale    │               │ ✓ Code partagé          │
└─────────────────────┘               └─────────────────────────┘

Usage:                                Usage:
gererVol(new Avion()) ✓               const chien = new Chien("Rex") ✓
gererVol(objetLitteral) ✓             chien.manger() // hérité ✓
```

## Approche JavaScript moderne avec Symbol

#### Schéma avec Symbol :
```
    Symbol('Volant') 
    ┌─────────────────┐
    │ Clé unique      │
    │ Non énumérable  │  
    │ Sécurisé        │
    └─────────────────┘
           │
           │ utilisé comme
           ▼
    ┌─────────────────────────────┐
    │ BaseVolant                  │
    │                             │
    │ constructor() {             │
    │   if (!this[VolantInterface])│ ← Vérification
    │     throw Error             │
    │ }                           │
    └─────────────────────────────┘
              │
              │ extends
              ▼
    ┌─────────────────────────────┐
    │ Drone extends BaseVolant    │
    │                             │
    │ constructor() {             │
    │   this[VolantInterface] = true │ ← Marque implémentation
    │   super() ✓                 │
    │ }                           │
    │                             │
    │ voler() { ... } ✓           │
    │ atterrir() { ... } ✓        │  
    │ altitude() { ... } ✓        │
    └─────────────────────────────┘

    Avantages Symbol:
    • Plus difficile à contourner
    • Clé unique garantie
    • Propriété cachée
```

```javascript
// Utilisation de Symbol pour créer des "interfaces" plus robustes
const VolantInterface = Symbol('Volant');

class BaseVolant {
    constructor() {
        if (!this[VolantInterface]) {
            throw new Error("Cette classe doit implémenter l'interface Volant");
        }
    }
}

class Drone extends BaseVolant {
    constructor() {
        this[VolantInterface] = true; // Marque qu'on implémente l'interface
        super();
        this.altitudeActuelle = 0;
    }
    
    voler() {
        console.log("Le drone s'élève");
        this.altitudeActuelle = 200;
    }
    
    atterrir() {
        console.log("Le drone se pose");
        this.altitudeActuelle = 0;
    }
    
    altitude() {
        return this.altitudeActuelle;
    }
}

const drone = new Drone(); // ✅ Fonctionne car il implémente l'interface
```

## Conclusion

#### Schéma récapitulatif des approches JavaScript :

```
                    INTERFACES EN JAVASCRIPT
                            │
            ┌───────────────┼───────────────┐
            │               │               │
            ▼               ▼               ▼
    
    DUCK TYPING        MIXINS         SIMULATION STRICTE
    ┌─────────────┐   ┌─────────────┐   ┌─────────────┐
    │ Naturel JS  │   │ Object.     │   │ Symbol +    │
    │ Flexible    │   │ assign()    │   │ Vérification│
    │ Simple      │   │ Modulaire   │   │ Robuste     │
    └─────────────┘   └─────────────┘   └─────────────┘
            │               │               │
            ▼               ▼               ▼
    
    typeof check      Erreurs par      Erreurs à la
    à l'exécution     défaut +         construction
                      redéfinition
    
    ┌─────────────────────────────────────────────────┐
    │                CLASSES ABSTRAITES                │
    │                                                 │
    │  ┌─────────────┐    extends    ┌─────────────┐   │
    │  │ Code        │ ──────────► │ Classe      │   │
    │  │ partagé +   │             │ concrète    │   │
    │  │ Méthodes    │             │             │   │
    │  │ abstraites  │             │             │   │
    │  └─────────────┘             └─────────────┘   │
    └─────────────────────────────────────────────────┘

    RECOMMANDATION :
    • Petits projets → Duck Typing
    • Projets moyens → Mixins  
    • Gros projets → Symbol + Vérifications
    • Code partagé → Classes abstraites
```

En JavaScript :
- **Pas d'interfaces natives** mais on peut simuler le concept
- **Duck Typing** est l'approche naturelle
- **Classes abstraites** peuvent être simulées
- **Mixins** offrent une alternative flexible
- **Symbol** permet des vérifications plus robustes

L'important est de maintenir la **cohérence** dans votre code et de documenter vos "contrats" même s'ils ne sont pas formellement définis par le langage.