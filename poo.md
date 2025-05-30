# Partie 1 : JavaScript et la POO

## JavaScript est-il un langage Orienté Objet ?

Réponse courte : Oui et non. JavaScript est un langage multi-paradigme qui supporte la POO, mais différemment des langages comme Java ou C#.

### Particularités de JavaScript :

- Basé sur les prototypes (prototype-based) plutôt que sur les classes pures
- Support des classes depuis ES6 (2015), mais c'est du "sucre syntaxique" au-dessus des prototypes
- Permet la POO, mais aussi la programmation fonctionnelle et procédurale

```javascript
// Approche prototype (JavaScript classique)
function Voiture(marque, modele) {
    this.marque = marque;
    this.modele = modele;
}
Voiture.prototype.demarrer = function() {
    return `${this.marque} ${this.modele} démarre !`;
};

// Approche classe (ES6+)
class Voiture {
    constructor(marque, modele) {
        this.marque = marque;
        this.modele = modele;
    }
    
    demarrer() {
        return `${this.marque} ${this.modele} démarre !`;
    }
}
```
Comparaison avec d'autres langages

Aspect|JavaScript|Java/C#|
|---|---|---|
|Base|Prototypes|Classes pures|
|Syntaxe classes|ES6+ (optionnelle)|Obligatoire|
|Types|Dynamique|Statique|
|Héritage|Prototypal + classes|Classes uniquement|
|Flexibilité|Très flexible|Plus rigide|
Conclusion : JavaScript supporte pleinement la POO moderne avec une approche unique et flexible.
