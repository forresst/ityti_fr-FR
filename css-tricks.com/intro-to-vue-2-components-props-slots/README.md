# Intro à Vue.js : Composants, Props et Slots

>**REMARQUE** : Cet article est une traduction en français de l'excellent article ["Intro to Vue.js: Components, Props, and Slots"](https://css-tricks.com/intro-to-vue-2-components-props-slots/) écrit par [Sarah Drasner](https://github.com/sdras)

## Introduction

Il s’agit du deuxième volet d’une série en cinq parties sur le framework JavaScript [Vue.js](https://vuejs.org/). Dans cette partie, nous allons passer en revue les composants, les Props et les Slots. Il ne s'agit pas d'un guide complet, mais plutôt d'un aperçu des principes fondamentaux pour vous aider, pour connaître Vue.js et comprendre ce que peut vous offrir le framework.

> Série d'articles :
>  1. [Rendu, directives et événements](../intro-to-vue-1-rendering-directives-events)
>  2. Composants, Props et Slots (Ce document !)
>  3. [Vue-cli](../intro-to-vue-3-vue-cli-lifecycle-hooks)
>  4. [Vuex](../intro-to-vue-4-vuex)
>  5. [Animations](../intro-to-vue-5-animations)

## Composants et passage de données

Si vous êtes familier avec React ou Angular2, l'idée de composants et de passage d'état ne vous sera pas inconnu. Si vous n'êtes pas dans ce cas, voyons quelques concepts principaux.

Les grands et les petits sites web sont généralement composés de plusieurs morceaux. Le découpage en petits morceaux permet de mieux structurer, de réutiliser et de rendre notre code plus lisible. Au lieu de mettre tout le balisage dans une longue page multifacette, nous pourrions le constituer de plusieurs composants comme ceci :

```HTML
<header></header>
<aside>
	<sidebar-item v-for="item in items"></sidebar-item>
</aside>
<main>
	<blogpost v-for="post in posts"></blogpost>
</main>
<footer></footer>
```

C'est un exemple simplifié, mais vous pouvez voir à quel point ce type de composition peut être utile lorsque vous commencez à construire la structure de votre site. Si vous deviez plonger dans ce code en tant que mainteneur, vous comprendriez vite : comment l'application est structurée et où chercher chaque morceau.

Vue nous permet de créer des composants de différentes façons. Nous irons du plus simple au plus complexe, en gardant à l'esprit que l'exemple le plus complexe est celui qui sera le plus souvent utilisé avec Vue dans une application moyenne.

JS :
```javascript
app.$mount('#app');

var app = new Vue({
	el: 'hello',
	template: '<h1>Hello World!</h1>'
});
```

Cela fonctionne, mais ce n’est pas très utile, car ça ne peut être utilisé qu'une seule fois et que nous ne transmettons pas encore l’information aux différents composants. Une façon de transférer des données d’un parent à un enfant est appelé **props**.

C'est un exemple simple et super clair, comme j'aurais l'habitude de le faire. N’oubliez pas que `:text` dans du HTML est un raccourci pour la liaison de Vue. Nous avons traité cela dans la dernière section sur les directives du précédent volet. La liaison peut être utilisée pour toutes sortes de choses, mais dans ce cas, elle nous empêche de placer un état (state) dans le template mustache, tel que `{{ message }}`.

Dans le code ci-dessous, `Vue.component` est le **composant** et `new Vue` est appelé **instance**. Vous pouvez avoir plus d'une instance dans une application. En règle générale, nous aurons une instance et plusieurs composants, car l'instance est l'application principale.

JS :
```javascript
Vue.component('child', {
	props: ['text'],
	template: `<div>{{ text }}<div>`
});

new Vue({
	el: "#app",
	data() {
		return {
			message: 'hello mr. magoo'
		}
	}
});
```

HTML :
```HTML
<div id="app">
	<child :text="message"></child>
</div>
```

*[Démo](http://codepen.io/sdras/pen/788a6a21e95589098af070c321214b78)*

Maintenant, nous pouvons réutiliser ce composant autant de fois que nous le souhaitons dans notre application :

HTML :
```HTML
<div id="app">
	<child :text="message"></child>
	<child :text="message"></child>
</div>
```

*[Démo](http://codepen.io/sdras/pen/9c04bdcf1a2d0c770d6a1fd4af3c66f3)*

Nous pouvons également ajouter la validation à nos props, qui ressemble à `PropTypes` dans React. C'est sympa car c'est de l'auto-documentation et cela rendra une erreur si ce n'est pas ce que nous attendions, mais seulement en mode développement :

JS :
```javascript
Vue.component('child', {
	props: {
		text: {
			type: String,
			required: true
		}
	},
	template: `<div>{{ text }}<div>`
});
```

Dans l'exemple ci-dessous, je charge Vue en mode développement et je renvoie intentionnellement un type non valide à notre validation du prop. **Vous pouvez voir l’erreur dans la console** (C'est utile, cela vous permet de savoir que vous pouvez utiliser les devtools de Vue et où les trouver).

JS :
```javascript
Vue.component('child', {
	props: {
		text: {
			type: Boolean,
			required: true
		}
	},
	template: `<div>{{ text }}<div>`
});
```

*[Démo](http://codepen.io/sdras/pen/d6fcaeee50a530d9a5e5832f0aec0773)*

Les objets doivent être retournés comme une fonction de factory et vous pouvez même les passer en tant que fonction de validation personnalisée, ce qui est vraiment agréable parce que vous pouvez vérifier les valeurs par rapport aux besoins, aux saisies et aux autres logiques. Il y a une belle description sur la manière d'utiliser chaque type [dans le guide](https://vuejs.org/v2/guide/components.html#Prop-Validation).

Vous n'avez pas besoin de passer nécessairement les données à l'enfant via les props, vous avez la possibilité d'utiliser soit l'état ou soit une valeur statique comme bon vous semble :

JS :
```javascript
Vue.component('child', {
	props: {
		count: {
			type: Number,
			required: true
		}
	},
	template: `<div class="num">{{ count }}</div>`
})

new Vue({
	el: '#app',
	data() {
		return {
			count: 0
		}
	},
	methods: {
		increment() {
			this.count++;
		},
		decrement() {
			this.count--;
		}
	}
})
```

HTML :
```HTML
<div id="app">
	<h3>
		<button @click="increment">+</button>
		Adjust the state
		<button @click="decrement">-</button>
	</h3>
	<h2>This is the app state: <span class="num">{{ count }}</span></h2>
	<hr>
	<h4><child count="1"></child></h4>
	<p>This is a child counter that is using a static integer as props</p>
	<hr>
	<h4><child :count="count"></child></h4>
	<p>This is the same child counter and it is using the state as props</p>
</div>
```

*[Démo](http://codepen.io/sdras/pen/5a34f6ed12cf954202c6d38f1ceba633)*

La différence est de savoir si vous passez et lier une propriété

**sans utiliser l'état (state)**
`<child count="1"></child>`

ou

**en utilisant l'état (state)**
`<child :count="count"></child>`

Jusqu'à présent, nous avons créé du contenu dans notre composant enfant avec un string, et bien sûr si vous utilisez babel pour pouvoir traiter ES6 dans tous les navigateurs (ce que je suggère fortement), vous pouvez utiliser un [template littéral](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals) pour éviter la concaténation de string potentiellement difficile à lire :

JS :
```javascript
Vue.component('individual-comment', {
	template:
	`<li> {{ commentpost }} </li>`,
	props: ['commentpost']
});

new Vue({
	el: '#app',
	data: {
		newComment: '',
		comments: [
			'Looks great Julianne!',
			'I love the sea',
			'Where are you at?'
		]
	},
	methods: {
		addComment: function () {
			this.comments.push(this.newComment)
			this.newComment = ''
		}
	}
});
```

HTML :
```HTML
<ul>
	<li
		is="individual-comment"
		v-for="comment in comments"
		v-bind:commentpost="comment"
	></li>
</ul>
<input
	v-model="newComment"
	v-on:keyup.enter="addComment"
	placeholder="Add a comment"
>
```

*[Démo](http://codepen.io/sdras/pen/cd81de1463229a9612dca7559dd666e0)*

C'est un peu plus utile, mais il y a encore une limite au nombre de contenu que nous voulons probablement mettre dans cette chaîne, même avec l'aide de templates littéraux. Finalement, dans ce formulaire de commentaire, nous aimerions avoir des photos et les noms des auteurs et vous pouvez déjà sans doute deviner l'encombrement avec toutes ces informations. Nous n'aurons pas non plus de marquage de syntaxe utile dans cette chaine.

Avec toutes ces choses à l’esprit, nous allons créer un template. Nous envelopperons du HTML ordinaire dans des balises de script spéciales et nous utiliserons un id pour référencer et créer un composant. Vous pouvez voir que cela est beaucoup plus lisible quand nous avons beaucoup de texte et d'éléments :

HTML :
```HTML
<!-- Il s'agit du composant individuel de commentaires -->
<script type="text/x-template" id="comment-template">
<li>
	<img class="post-img" :src="commentpost.authorImg" />
	<small>{{ commentpost.author }}</small>
	<p class="post-comment">"{{ commentpost.text }}"</p>
</li>
</script>
```

JS :
```javascript
Vue.component('individual-comment', {
	template: '#comment-template',
	props: ['commentpost']
});
```

*[Démo](http://codepen.io/sdras/pen/egEgXb)*

## Slots

C'est beaucoup mieux. Mais que se passe-t-il lorsque nous avons deux composantes avec de légères variations, soit de contenu ou de style ? Nous pourrions passer toutes les différences de contenu et de style dans le composant avec des props et tout changer à chaque fois, ou, nous pourrions dupliquer les composants eux-mêmes et en créer différentes versions. Mais ce serait vraiment bien si nous pouvions réutiliser les composants et les remplir avec les mêmes données ou fonctionnalités. C'est là que les slots sont vraiment pratiques.

Disons que nous avons une instance d'une application principale utilisant deux fois le même composant `<app-child>`. À l’intérieur de chaque enfant, nous voulons le même contenu et un contenu différent. Pour le contenu que nous voulons identique, nous utiliserons une balise standard p, et pour le contenu que nous voulons changer, nous allons mettre une balise `<slot></slot>` vide.

HTML :
```HTML
<script type="text/x-template" id="childarea">
	<div class="child">
		<slot></slot>
		<p>It's a veritable slot machine!<br>
		Ha ha aw</p>
	</div>
</script>
```

Ensuite, dans l'instance de l'application, nous pouvons transmettre du contenu à l'intérieur des balises de composant `<app-child>` et il remplira automatiquement les slots :

HTML :
```HTML
<div id="app">
	<h2>We can use slots to populate content</h2>
	<app-child>
		<h3>This is slot number one</h3>
	</app-child>
	<app-child>
		<h3>This is slot number two</h3>
		<small>I can put more info in, too!</small>
	</app-child>
</div>
```

*[Démo](http://codepen.io/sdras/pen/e06f9393e73054e7185ff48dfa36e161)*

Vous pouvez également avoir un contenu par défaut dans les slots. Si, dans le slot lui-même, plutôt que d'écrire `<slot></slot>`, vous pouvez le remplir avec :

`<slot>Je suis un texte par défaut</slot>`

Ce texte par défaut sera utilisé jusqu'à ce que vous remplissiez le slot avec autre chose, ce qui est très utile !

Vous pouvez avoir également des slots nommés. Si vous avez deux slots dans un composant, vous pouvez les différencier en ajoutant un attribut name `<slot name="headerinfo"></slot>` et nous pourrons accéder à ce slot particulier en écrivant `<h1 slot="headerinfo">Je vais remplir l'entête du slot !</h1>`. C'est extrêmement utile. Si vous avez plusieurs slots qui sont nommés et un qui ne l'est pas, Vue mettra le contenu nommé dans les slots nommés, et ce qui reste sera utilisé pour remplir le slots sans nom.

Voici un exemple de ce que je viens de dire :

**Il s'agit d'un exemple de template enfant**

HTML :
```HTML
<div id="post">
	<main>
		<slot name="header"></slot>
		<slot></slot>
	</main>
</div>
```

**Il s'agit d'un exemple de parent**

HTML :
```HTML
<app-post>
	<h1 slot="header">C'est le titre principal</h1>
	<p>Je vais dans le slot sans nom !</p>
</app-post>
```

**Le contenu qui est rendu**

HTML :
```HTML
<main>
	<h1>C'est le titre principal</h1>
	<p>Je vais dans le slot sans nom !</p>
</main>
```

Personnellement, si j'utilise plus d'un slot à la fois, je les nommerai tous afin que ce soit super clair pour d'autres mainteneurs, mais c'est agréable que Vue fournisse une telle API.

### Slots example

Alternativement, nous pouvons avoir des styles particuliers affectés à différents composants et garder tout le contenu à l'intérieur du même, ce qui permet de changer rapidement et facilement l'apparence de quelque chose. L'application pour fabriquer une étiquette de vin ci-dessous, a l'un de ses boutons qui permet de changer le composant et la couleur, suivant ce que l'utilisateur sélectionne. L'arrière-plan de la bouteille, l'étiquette et le texte changeront tous en même temps, tout en gardant le contenu stable.

JS :
```javascript
const app = new Vue({
	...
	components: {
		'appBlack': {
		template: '#black'
		}
	}
});
```

HTML principal de l'application Vue :

HTML :
```HTML
	<component :is="selected">
		...
		<path class="label" d="M12,295.9s56.5,5,137.6,0V409S78.1,423.6,12,409Z" transform="translate(-12 -13.8)" :style="{ fill: labelColor }"/>
		...
	</component>

<h4>Couleur</h4>
	<button @click="selected ='appNoir', labelColor = '#000000'">Etiquette noire</button>
	<button @click="selected ='appBlanc', labelColor = '#ffffff'">Etiquette blanche</button>
	<input type="color" v-model="labelColor" defaultValue="#ff0000">
```

HTML du composant blanc :

HTML :
```HTML
<script type="text/x-template" id="blanc">
	<div class="blanc">
		<slot></slot>
	</div>
</script>
```

(Il s’agit d’une démo plus grande, donc ce serait plus judicieux de jouer avec elle dans une fenêtre/onglet séparé)

*[Démo](http://codepen.io/sdras/pen/BpjQzE)*

![Démo application](assets/wine-label1.gif)

Maintenant, nous mettons toutes les données des images SVG dans l’application principale, mais il est en fait placé à l’intérieur du `<slot>` dans chaque composant. Cela nous permet de changer des morceaux de contenu ou des choses de style différentes selon l’usage, c'est une fonctionnalité vraiment intéressante. Vous pouvez voir que nous avons permis à l’utilisateur de décider quel composant il utilisera en créant un bouton qui change la valeur "sélectionnée" (selected) du composant.

À l'heure actuelle, nous avons tout dans un slot, mais nous pourrions également utiliser des slots multiples et les différencier en les nommant :

HTML :
```HTML
<!-- Instance de l'application principale vue -->
<app-comment>
	<p slot="comment">{{ comment.text }}</p>
</app-comment>

<!-- Composant individuel -->
<script type="text/x-template" id="comment-template">
	<div>
		<slot name="comment"></slot>
	</div>
</script>
```

Nous pouvons facilement basculer entre les différentes composantes avec les mêmes slots référencés, mais que se passe-t-il lorsque nous voulons revenir en arrière, mais gardez l’état individuel de chaque composant ? Actuellement, quand on passe du noir au blanc, les templates changent mais le contenu reste le même. Mais peut-être que nous avons une situation où nous voulons que l’étiquette noire soit complètement différente de la blanche. Il y a un composant spécial, qui enveloppe, appelé `<keep-alive></keep-alive>`, qui conservera l'état que vous basculez.

Vérifiez cette modification dans l'exemple ci-dessus - créez une étiquette noire, puis une étiquette blanche différente, et basculez-vous entre les deux. Vous verrez que l'état de chacune est préservé et sont différentes les unes des autres :

HTML :
```HTML
<keep-alive>
	<component :is="selected">
		...
	</component>
</keep-alive>
```

![Démo application 2](assets/wine-label2.gif)

*[Démo](http://codepen.io/sdras/pen/db71c231f760ee3a53e9d4e65f8745b8)*

J'adore cette fonctionnalité de l'API.

Tout cela est bien, mais pour simplifier, nous avons tout collé dans un ou deux fichiers. Ce serait beaucoup mieux organisé, si pendant que nous construisons notre site, nous pouvions séparer les composants dans différents fichiers. Puis les importer lorsque nous en avons besoin, et c'est vraiment ainsi que le développement réel dans Vue est généralement fait. Nous verrons cela dans le prochain volet. La prochaine partie, nous parlerons de Vue-cli pour construire des processus et de Vuex pour la gestion de l'état !

> Série d'articles :
>  1. [Rendu, directives et événements](../intro-to-vue-1-rendering-directives-events)
>  2. Composants, Props et Slots (Ce document !)
>  3. [Vue-cli](../intro-to-vue-3-vue-cli-lifecycle-hooks)
>  4. [Vuex](../intro-to-vue-4-vuex)
>  5. [Animations](../intro-to-vue-5-animations)
