# Intro à Vue.js : Rendu, directives et événements

>**REMARQUE** : Cet article est une traduction en français de l'excellent article ["Intro to Vue.js: Rendering, Directives, and Events"](https://css-tricks.com/intro-to-vue-1-rendering-directives-events/) écrit par [Sarah Drasner](https://github.com/sdras)

## Introduction

Si je devais résumer, en une phrase, mon expérience avec [Vue](https://vuejs.org/), je dirais sans doute quelque chose comme "Il est tellement suffisant" ou "Il me donne les outils que je veux quand je veux, et il ne se met jamais en travers de mon chemin". À plusieurs reprises, en apprenant Vue, je me suis souri. C'est juste logique et judicieux.

C'est ma propre introduction sur Vue. C'est l'article que j'aurais aimé lire quand j'ai appris Vue. Si vous souhaitez une approche plus impartiale, veuillez visiter le site de Vue qui est très bien pensé et facile en suivant le [Guide](https://vuejs.org/v2/guide/).

> Série d'article :
>  1. Rendu, directives et événements (Ce document !)
>  2. [Composants, Props et Slots](../intro-to-vue-2-components-props-slots)
>  3. [Vue-cli](../intro-to-vue-3-vue-cli-lifecycle-hooks)
>  4. [Vuex](../intro-to-vue-4-vuex)
>  5. [Animations](../intro-to-vue-5-animations)

L'une des choses que j'ai préféré de Vue, c'est qu'il a récupéré toutes les choses réussies des autres frameworks et les a incorporés de façon méthodique. Voici quelques exemples importants pour moi :
* Un DOM virtuel avec des composants réactifs qui proposent uniquement la couche de la Vue, des *props* et un *store* d'un Redux-like similaire à React.
* Un rendu conditionnel et des services, semblables à Angular.
* Inspiré par Polymer en partie en termes de simplicité et de performance, Vue offre un style de développement similaire à celui du HTML, des styles et du JavaScript qui sont composés de pair.

Face aux concurrents, Vue a quelques avantages que j'ai apprécié : plus propre, plus d'offres d'API sémantique, une performance légèrement meilleure que React, aucune utilisation de polyfills comme Polymer, une Vue isolée et moins opiniâtre qu'Angular qui est un MVC.

Je pourrais poursuivre, mais il serait préférable que vous lisiez [la comparaison](https://vuejs.org/v2/guide/comparison.html) complète et axée sur la communauté avec d'autres frameworks. Elle vaut la peine d'être lue, mais vous pouvez y revenir plus tard si vous souhaitez plonger dans le code.

## Commençons !

Nous ne pouvons pas commencer sans faire l'exemple "Hello, world!". Faisons-le pour que vous puissiez vous lancer :

HTML :
```HTML
<div id="app">
	{{ text }} Nice to meet Vue.
</div>
```

JS :
```javascript
new Vue({
	el: '#app',
	data: {
		text: 'Hello World!'
	}
});
```

*[Démo](http://codepen.io/sdras/pen/b52b1252469a353830683aeaccbecd01) sur CodePen*

Si vous êtes familier avec React, vous verrez quelques similitudes. Nous avons sorti vers le code JavaScript une partie du contenu avec un template de mustache et utilisé une variable, mais l'une des différences, c'est que nous travaillons directement avec du HTML au lieu de [JSX](https://facebook.github.io/react/docs/introducing-jsx.html). JSX est assez facile à utiliser, mais je crois vraiment qu'il est agréable de ne pas passer du temps à changer `class` en `className`, etc. Vous remarquerez également que c'est assez léger pour démarrer.

Maintenant, essayons Vue avec quelque chose que j'aime vraiment : les boucles et le rendu conditionnel.

## Rendu conditionnel

Disons que j'ai un ensemble d'éléments, comme un menu, que je sais que je vais réutiliser. Il serait judicieux de le mettre dans un tableau pour l'actualiser à certains endroits de manière dynamique et cohérente. En vanilla JS (avec Babel), nous pourrions faire quelque chose comme cela : créer le tableau, puis créer une chaîne vide où nous ajoutons chaque élément dans un `<li>`, envelopper le tout dans un `<ul>` et l'ajouter au DOM avec `innerHTML` :

HTML :
```HTML
<div id="container"></div>
```

JS :
```javascript
const items = [
	'thingie',
	'another thingie',
	'lots of stuff',
	'yadda yadda'
];

function listOfStuff() {
	let full_list = '';
	for (let i = 0; i < items.length; i++) {
		full_list = full_list + `<li> ${items[i]} </li>`
	}
	const contain = document.querySelector('#container');
	contain.innerHTML = `<ul> ${full_list} </ul>`;
}

listOfStuff();
```

*[Démo](http://codepen.io/sdras/pen/e699f60b79b90a35401cc2bcbc588159) sur CodePen*

Cela fonctionne très bien, mais c’est un peu brouillon pour quelque chose de standard. Maintenant, nous allons implémenter la même chose avec la boucle `v-for` de Vue :

HTML :
```HTML
<div id="app">
	<ul>
		<li v-for="item in items">
			{{ item }}
		</li>
	</ul>
</div>
```

JS :
```javascript
const app4 = new Vue({
	el: '#app',
	data: {
		items: [
			'thingie',
			'another thingie',
			'lots of stuff',
			'yadda yadda'
		]
	}
});
```

*[Démo](http://codepen.io/sdras/pen/af6307c633262350c9642f554ff64b55) sur CodePen*

C'est assez propre et déclaratif. Si vous êtes familier avec Angular, cela vous sera probablement familier. Je trouve que c'est une façon propre et lisible de rendre le contenu de manière conditionnelle. Si vous deviez sauter dans le code et le mettre à jour, vous pourriez le faire très facilement.

Un autre point vraiment sympa, c'est la liaison dynamique avec `v-model`. Regardez ceci :

HTML :
```HTML
<div id="app">
	<h3>Type here:</h3>
	<textarea v-model="message" class="message" rows="5" maxlength="72"></textarea><br>
	<p class="booktext">{{ message }} </p>
</div>
```

JS :
```javascript
new Vue({
	el: '#app',
	data() {
		return {
			message: 'This is a good place to type things.'
		}
	}
});
```

*[Démo](http://codepen.io/sdras/pen/fc5a128716814995b888d362a5e1b367) sur CodePen*

Vous remarquez probablement deux choses au sujet de cette démo. Tout d'abord, qu’il n'y a rien à taper directement dans le livre et que dynamiquement le texte se met à jour. Vue nous permet de créer très facilement une liaison bidirectionnelle entre `<textarea>` et `<p>` avec `v-model`.

L'autre chose que vous pourriez remarquer, c'est que nous mettons maintenant des données dans une fonction. Dans cet exemple, cela fonctionnerait sans le faire ainsi. Nous aurions pu le mettre dans un objet comme dans nos exemples précédents. Mais cela fonctionnerait uniquement pour l'instance de Vue et ce serait exactement la même donnée à travers toute l'application (donc c'est pas très bien pour les composants individuels). C'est OK pour une instance de Vue, mais cela permettra également de partager des données sur tous les composants enfants. Il est conseillé de commencer à mettre des données dans une fonction, car nous en aurons besoin quand nous commencerons à utiliser les composants et que nous voudrons leur garder leur propre état (state).

Ce ne sont pas les seules liaisons de saisies à votre disposition, même `v-if` a un remplaçant : `v-show`, qui ne montera/démontera pas le composant, mais plutôt, le laissera dans le DOM et activera/désactivera sa visibilité.

Il y a [tellement de directives](https://vuejs.org/v2/api/#Directives) à votre disposition, voici quelques exemples de celles que j'utilise très souvent. Il y a aussi beaucoup raccourcis, donc je vais montrer les deux. À partir de là, nous allons surtout utiliser les raccourcis, donc il est bon de vous familiariser avec eux depuis ce tableau.

Nom | Raccourci | Objectif | Exemple
----|-----------|----------|--------
v-if, v-else-if, v-else | aucun | Rendu conditionnel | `<g v-if="flourish === 'A'"></g><g v-else-if="flourish === 'B'"></g><g v-else></g>`
v-bind | : | Lie dynamiquement les attributs ou passe les props | `<div :style="{ background: color }"></div>`
v-on | @ | Attache un écouteur (listener) d'événements à l'élément | `<button @click="fnName"></button>`
v-model | aucun | Crée une liaison bidirectionnelle | `<textarea rows="5" v-model="message" maxlength="72"></textarea>`
v-pre | aucun | Saute la compilation pour le contenu brut, peut améliorer les performances | `<div v-pre>{{ contenu brut avec aucune méthode}}</div>`
v-once | aucun | Ne pas re-rendre | `<div class=”v-once”>Ne fait pas de re-rendu</div>`
v-show | aucun | Affichera ou masquera un composant/élément en fonction de state, mais il le laissera dans le DOM sans démontage (contrairement à v-if) | `<child v-show=”showComponent”></child>` (Active la visibilité lorsque showComponent est vrai)

Il y a aussi des modificateurs d'événements très agréables et d'autres API pour accélérer le développement comme :
* `@mousemove.stop` est comparable à `e.stopPropogation()`
* `@mousemove.prevent` c'est comme `e.preventDefault()`
* `@submit.prevent` Ca ne rechargera plus la page à la soumission (submit)
* `@click.once` à ne pas confondre avec v-once, cet *événement click* sera déclenché une fois.
* `v-model.lazy` ne remplira pas le contenu automatiquement, il attendra pour le lier qu'un événement arrive.

Vous pouvez même [configurer vos propres codes clavier](https://vuejs.org/v2/api/#keyCodes).

Nous les utiliserons dans de futurs exemples !

## La gestion de l'évènement

La liaison, c'est bien mais nous obtenons cela sans manipuler jusqu'à présent d'événement, c'est ce que nous allons découvrir maintenant ! C'est l'une de mes parties préférées. Nous utiliserons la liaison et les écouteurs ci-dessus pour écouter les événements du DOM.

Il existe plusieurs façons de créer des méthodes utilisables dans notre application. Comme avec du vanilla JS, vous pouvez choisir vos noms de fonction, mais les méthodes sont intuitivement appelées ainsi, donc `methods` !

JS :
```javascript
new Vue({
	el: '#app',
	data() {
		return {
			counter: 0
		}
	},
	methods: {
		increment() {
			this.counter++;
		}
	}
});
```

HTML :
```HTML
<div id="app">
	<p><button @click="increment">+</button> {{ counter }}</p>
</div>
```

Nous créons une méthode appelée `increment` et vous pouvez voir que cela se lie automatiquement à `this` et donc se référera aux données dans cette instance et ce composant. J'aime ce type de liaison automatique, c'est si agréable de ne pas avoir à faire un `console.log` pour voir à quoi se réfère `this`. Nous utilisons le raccourci `@click` pour lier ici l'évènement click.

Les méthodes ne sont pas la seule façon de créer une fonction personnalisée. Vous pouvez également utiliser `watch`. La principale différence : les méthodes sont bien pour les calculs qui sont petits et synchrones, tandis que `watch` est utile pour des opérations avec plus de tâches ou des opérations asynchrones ou couteuses en réponse dans le cas d'un échange de données. J'ai tendance à utiliser plus souvent `watch` avec les animations.

Allons plus loin et voyons comment nous passerions l'événement et comment nous ferions des liaisons de style dynamique. Si vous vous souvenez dans le précédent tableau, au lieu d'écrire `v-bind`, vous pouvez utiliser le raccourci `:`, donc nous pouvons lier assez facilement `style` (de la même manière que les autres attributs) en utilisant `:style` et en le passant dans l'état ou `:class`. Il y a vraiment beaucoup de cas d’utilisation pour ce type de liaison.

Dans l’exemple ci-dessous, nous utilisons `hsl()`, afin que la [teinte soit calculée comme une couleur sur un cercle chromatique](https://fr.wikipedia.org/wiki/Nuancier_de_Munsell) lorsque nous traversons l'écran. C'est idéal pour notre besoin parce que cela ne plantera jamais, ainsi en déplaçant notre souris sur l'écran, le style de l’arrière-plan se mettra à jour en conséquence. Nous utilisons ici les [littéraux de template de l'ES6](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Litt%C3%A9raux_gabarits).

JS :
```javascript
new Vue({
	el: '#app',
	data() {
		return {
			counter: 0,
			x: 0
		}
	},
	methods: {
		increment() {
			this.counter++;
		},
		decrement() {
			this.counter--;
		},
		xCoordinate(e) {
			this.x = e.clientX;
		}
	}
});
```

HTML :
```HTML
<div id="app" :style="{ backgroundColor: `hsl(${x}, 80%, 50%)` }" @mousemove="xCoordinate">
	<p><button @click="increment">+</button> {{ counter }} <button @click="decrement">-</button></p>
	<p>Pixels across: {{ x }}</p>
</div>
```

*[Démo](http://codepen.io/sdras/pen/75205908c2189487ca91f9b49c1c978a) sur CodePen*

Vous pouvez voir que nous avons pas eu besoin de passer l'événement au gestionnaire de `@click`, Vue le passe automatiquement pour vous et il sera disponible comme paramètre pour la méthode. (représenté ici par `e`).

De plus, les méthodes natives peuvent également être utilisées, telles que `event.clientX` et c'est simple de les associer aux instances de `this`. Dans la liaison de style sur l’élément, on utilise une annotation CamelCase pour se référer aux propriétés CSS qui ont des tirets (`backgroundColor` et `background-color`). Dans cet exemple, vous pouvez voir comment Vue fonctionne avec des déclaratifs simples.

Nous n'avons pas vraiment besoin de créer une méthode pour tout, si l’événement est assez simple, nous pourrions également augmenter le compteur directement dans le HTML du composant :

HTML :
```HTML
<div id="app">
	<div class="item">
		<img src="https://s3-us-west-2.amazonaws.com/s.cdpn.io/28963/backpack.jpg" width="235" height="300"/>
		<div class="quantity">
			<button class="inc" @click="counter > 0 ? counter -= 1 : 0">-</button>
			<span class="quant-text">Quantity: {{ counter }}</span>
			<button class="inc" @click="counter += 1">+</button>
		</div>
		<button class="submit" @click="">Submit</button>
	</div><!--item-->
</div>
```

JS :
```javascript
new Vue({
	el: '#app',
	data() {
		return {
			counter: 0
		}
	}
});
```

*[Démo](http://codepen.io/sdras/pen/f979956bee610da7563db67b1358619f) sur CodePen*

Vous pouvez constater que nous mettons à jour l'état directement dans le gestionnaire de `@click` sans méthode - vous pouvez également voir que nous pouvons ajouter un peu de logique là aussi (car vous n'aurez jamais d'article en dessous de zéro sur un site d'achat). Dès que cette logique devient trop complexe, vous sacrifiez la lisibilité, donc il est bon de le déplacer dans une méthode. C'est bien d'avoir l'option pour l'un ou l'autre.

> Série d'article :
>  1. Rendu, directives et événements (Ce document !)
>  2. [Composants, Props et Slots](../intro-to-vue-2-components-props-slots)
>  3. [Vue-cli](../intro-to-vue-3-vue-cli-lifecycle-hooks)
>  4. [Vuex](../intro-to-vue-4-vuex)
>  5. [Animations](../intro-to-vue-5-animations)
