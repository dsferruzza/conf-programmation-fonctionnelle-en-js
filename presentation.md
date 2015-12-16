% La programmation fonctionnelle en JS
% David Sferruzza

# À propos de moi

- [\@d_sferruzza](https://twitter.com/d\_sferruzza)
- [github.com/dsferruzza](https://github.com/dsferruzza)
- développeur et responsable R&D chez [Escale](http://www.escaledigitale.com)
- doctorant en génie logiciel à l'Université de Nantes
- écrit des projets perso et pro en Scala et en Haskell (notamment) depuis ~ 2 an
- ce talk est inspiré d'un article que j'ai écrit pour [24 jours de web](http://www.24joursdeweb.fr/2014/un-peu-de-programmation-fonctionnelle-en-javascript)

![](img/escale.png)


# Qu'est-ce que la programmation fonctionnelle ?

![](img/suspicious.gif)


# Repères

Camembert > Programmation fonctionnelle

![](img/trends.png)

<figure><img src="img/troll.gif" alt="" width="300"></figure>


# FP is for Functional Programming

> [C'est] un **paradigme** de programmation qui considère le calcul en tant qu'**évaluation** de fonctions mathématiques.

> It is a **declarative** programming paradigm, which means programming is done with **expressions**.

<small>Sources : [Wikipedia FR](https://fr.wikipedia.org/wiki/Programmation_fonctionnelle) / [Wikipedia EN](https://en.wikipedia.org/wiki/Functional_programming)</small>


# Lambda calcul (λ calculus)

- Alonzo Church (1930s)
- système formel équivalent à la machine de Turing

![](img/gordon.png)

<small>Slides sympa : [The Lambda Calculus and The JavaScript](http://fr.slideshare.net/normanrichards/the-lambda-calculus-and-the-javascript)</small>


# Pourquoi apprendre FP ?

- prendre du recul sur la programmation
- agrandir **et** consolider sa zone de confort
- réutiliser les concepts intéressants pour faire de **meilleurs programmes**

> Apprendre Haskell permet d'écrire de meilleurs programmes, même dans d'autres langages.

![](img/promise.gif)


# Concepts

Aujourd'hui on va explorer des concepts vraiment cools issus de la programmation fonctionnelle moderne :

- transparence référentielle
- fonctions d’ordre supérieur
- évaluation paresseuse
- *immuabilité*
- *types de données algébriques*
- ...

et les appliquer en JS !


# Fonction impure

```javascript
function diviserParDeux(nombre) {
	var missile = lancerUnMissileNucleaire();
	fairePorterLeChapeauAuDrManhattan(missile);
	return nombre / 2;
}
```

Cette fonction a des **effets de bord** *observables* qu'on ne peut pas deviner en regardant sa valeur de retour.
Il faut donc faire constamment attention lorsqu'on la manipule !

<figure><img src="img/fail.gif" alt="" width="225"></figure>


# Fonction pure

```javascript
function ajouterCinq(nombre) {
	return nombre + 5;
}
```

Cette fonction n'a pas d'effets de bord. Elle renvoie **toujours** le même résultat lorsqu'on l'appelle avec les mêmes arguments.

```javascript
ajouterCinq(-4) === ajouterCinq(-4)
```

<figure><img src="img/excellent.gif" alt="" width="400"></figure>


# Transparence référentielle

> Le résultat du programme ne change pas si on remplace une expression par une expression de valeur équivalente.

Utiliser des fonctions pures permet de **raisonner** sur son programme comme sur une équation.

<figure><img src="img/mind_blown.gif" alt="" width="450"></figure>


# Recommandations

## Construire son programme avec un maximum de fonctions pures

La logique métier est fiable, sans surprise et aisément testable.

## Isoler les fonctions qui ont des effets de bord

On sait quelles fonctions ont des effets de bord, ce qui permet d’être prudent lorsqu’on les manipule.


# Concepts

- ~~transparence référentielle~~
- fonctions d’ordre supérieur
- évaluation paresseuse
- *immuabilité*
- *types de données algébriques*


# Fonction d'ordre supérieur

En JS, les fonctions sont des objets de première classe (*first-class citizen*).

Une fonction d’ordre supérieur est une fonction qui possède au moins l'une des propriétés suivantes :

- elle accepte au moins une autre fonction en paramètre
- elle retourne une fonction en résultat

<figure><img src="img/first_class.gif" alt="" width="305"></figure>


# Exemple de fonction d'ordre supérieur

```javascript
function appliquerDeuxFois(f, x) {
	return f(f(x));
}
// ^ accepte une fonction en paramètre

function foisTrois(x) {
	return x * 3;
}

appliquerDeuxFois(foisTrois, 7);
// --> (7 * 3) * 3 = 63
```


# Intérêt des fonctions d'ordre supérieur

- bien séparer les différentes tâches effectuées par nos fonctions
- écrire des fonctions composables, modulables, paramétrables

<figure><img src="img/inception.gif" alt="" width="550"></figure>


# Exemple !!

On va voir un exemple concret : les opérations sur les tableaux en JS.

![](img/party.gif)


# Situation

```javascript
var villes = [
	{ nom: "Nantes", dep: 44, mer: false },
	{ nom: "Dunkerque", dep: 59, mer: true },
	{ nom: "Paris", dep: 75, mer: false },
];
```

On veut obtenir une liste de chaines de type `ville (dep)`.

On va écrire une fonction `rendreVillesAffichables` telle que :

```javascript
rendreVillesAffichables(villes);
// --> ["Nantes (44)", "Dunkerque (59)",
//      "Paris (75)"]
```


# Solution impérative

```javascript
function rendreVillesAffichables(villes) {
	for (var i = 0; i < villes.length; i++) {
		villes[i] = villes[i].nom +
					" (" + villes[i].dep + ")";
	}
	return villes;
}
```

On mélange 2 comportements :

- parcourir le tableau
- transformer les données


# Array.map

```javascript
function rendreVillesAffichables(villes) {
	function transformation(ville) {
		return ville.nom +
				" (" + ville.dep + ")";
	}
	return villes.map(transformation);
}
```

- `Array.map` gère le parcours du tableau \\o/
- on gère la transformation


# Syntaxe ES6

Avec les *lambdas* et les *template strings* c'est encore plus clair !

```javascript
villes.map(v => `${v.nom} (${v.dep})`);
// --> ["Nantes (44)", "Dunkerque (59)",
//      "Paris (75)"]
```

![](img/reindeer.gif)


# Array.filter

```javascript
villes.filter(function(item) {
	// Si la ville est près de la mer,
	// on renvoie true, sinon false
	return item.mer;
});
// --> [ { nom: "Dunkerque", dep: 59,
//			mer: true }
```


# Chainons !

```javascript
villes.filter(function(item) {
	return !item.mer;
}).map(function(item) {
	return item.nom + " (" + item.dep + ")";
});
// --> ["Nantes (44)", "Paris (75)"]
```

*Rappel : `Array.map` et `Array.filter` sont des fonctions d'ordre supérieur.*


# Array.reduce

```javascript
var personnes = [
	{ nom: "Bruce", age: 30 },
	{ nom: "Tony", age: 35 },
	{ nom: "Peter", age: 26 },
];

personnes.reduce(function(acc, cur) {
	return acc + cur.age;
}, 0);
// --> 91
```


# Array.reduce

```javascript
function map(tableau, transformation) {
	return tableau.reduce(function(acc, cur) {
		acc.push(transformation(cur));
		return acc;
	}, []);
}

function filter(tableau, predicat) {
	return tableau.reduce(function(acc, cur) {
		if (predicat(cur)) acc.push(cur);
		return acc;
	}, []);
}
```


# Recommandations

Éviter d'utiliser des boucles pour manipuler des tableaux.

## Antisèche

Si vous avez un tableau et que vous voulez :

- appliquer une transformation sur chacune de ses cases (en conservant leur ordre/nombre) : **map**
- supprimer certaines cases (en conservant l’ordre et le contenu des autres) : **filter**
- le parcourir pour construire une nouvelle structure de données : **reduce**


# Concepts

- ~~transparence référentielle~~
- ~~fonctions d’ordre supérieur~~
- évaluation paresseuse
- *immuabilité*
- *types de données algébriques*


# Stratégie d'évaluation

- **quand** évaluer les arguments d'un appel de fonction
- **quel type de valeur** passer à la fonction

<figure><img src="img/bean.gif" alt="" width="600"></figure>


# Évaluation stricte

*strict evaluation, eager evaluation, greedy evaluation*

- **quand :** dès que l'expression peut être liée à une variable
- **quel type de valeurs :**
	- *call by value*
	- *call by reference*
	- *call by sharing*
	- ...


# Évaluation non stricte

*non-strict evaluation, lazy evaluation*

- **call by name :** les arguments sont substitués dans le corps de la fonction
- **call by need :** idem, avec *mémoïsation* (≈ mise en cache du résultat de l'évaluation des arguments)
- ...


# Évaluation paresseuse

L'exécution d'un bout de code ne se fait pas avant que les résultats de ce bout de code ne soient réellement nécessaires.

<figure><img src="img/clean.gif" alt="" width="515"></figure>


# À quoi ça sert ?

- **optimisation :** on peut éviter des calculs inutiles
- **maintenabilité :**
	- on peut exprimer des structures de données infinies
	- on peut définir des structures de contrôle comme des abstractions, au lieu de primitives


# Lo-Dash

> A JavaScript utility library delivering consistency, modularity, performance, & extras.

<https://lodash.com/>

- bibliothèque JS qui permet (notamment) de manipuler les collections
- *lazy* depuis la v3


# Exemple

```javascript
var t = [0, 1, 2, 3, 4];

function plusUn(nb) {
	console.log(nb + ' + 1');
	if (nb > 2) console.log('Traitement long');
	return nb + 1;
}

function petit(nb) {
	console.log(nb + ' plus petit que 3 ?');
	return nb < 3;
}
```


# Sans Lo-Dash

<div style="float: right; margin-right: 100px;">
```javascript
var js = t
		.map(plusUn)
		.filter(petit)
		.slice(0, 2);
```
</div>

```
0 + 1
1 + 1
2 + 1
3 + 1
Traitement long
4 + 1
Traitement long
1 plus petit que 3 ?
2 plus petit que 3 ?
3 plus petit que 3 ?
4 plus petit que 3 ?
5 plus petit que 3 ?
[ 1, 2 ]
```


# Sans Lo-Dash

<figure><img src="img/without-lodash.gif" alt="" width="690"></figure>


# Avec Lo-Dash

```javascript
var _ = require('lodash');
var lodash = _(t)
		.map(plusUn)
		.filter(petit)
		.take(2)
		.value();
```

```
0 + 1
1 plus petit que 3 ?
1 + 1
2 plus petit que 3 ?
[ 1, 2 ]
```


# Avec Lo-Dash

<figure><img src="img/with-lodash.gif" alt="" width="690"></figure>


# Évaluation paresseuse

- séparation
	- du Calcul, de la **génération**<br>*→ où le calcul d'une valeur est-il défini ?*
	- du Contrôle, de la **condition d'arrêt**<br>*→ où le calcul d'une valeur se produit-il ?*
- *colle* qui permet d'assembler efficacement des (bouts de) programmes : facilite l'approche *diviser pour régner*

**Avantages :** peut augmenter la maintenabilité *et* les performances

**Inconvénients :** peut introduire de l'*overhead* (dépend pas mal de la techno)


# Concepts

- ~~transparence référentielle~~
- ~~fonctions d’ordre supérieur~~
- ~~évaluation paresseuse~~
- *immuabilité*
- *types de données algébriques*
