# Intro à Vue.js : Rendu, directives et événements

>**REMARQUE** : Cet article est une traduction en français de l'excellent article ["Intro to Vue.js: Rendering, Directives, and Events"](https://css-tricks.com/intro-to-vue-1-rendering-directives-events/) écrit par [Sarah Drasner](https://github.com/sdras)

## Introduction

Si je devais résumer mon expérience avec [Vue](https://vuejs.org/) en une phrase, je dirais sans doute que quelque chose comme "Il est tellement suffisant" ou "Il me donne les outils que je veux quand je veux, et ne se met jamais en travers de mon chemin". À plusieurs reprises, en apprenant Vue, Je me suis souri. C'est juste logique et judicieux.

C'est ma propre introduction sur Vue. C'est l'article que j'aurais aimer avoir lu quand j'ai appris Vue. Si vous souhaitez une approche plus impartiale, veuillez visiter Vue qui est très bien pensé et facile en suivant le [Guide](https://vuejs.org/v2/guide/).

> Série d'article :
>  1. Rendu, directives et événements (Ce document !)
>  2. [Composants, Props et Slots](../intro-to-vue-2-components-props-slots)
>  3. Vue-cli
>  4. Vuex
>  5. Animations

L'une des choses que j'ai préféré de Vue, c'est qu'il a récupéré toutes les choses réussies des autres frameworks et les a incorporé de façon méthodique. Voici quelques exemples qui se démarquent pour moi :
* Un DOM virtuel avec des composants réactifs qui proposent uniquement la couche de la Vue, des *props* et un *store* d'un Redux-like similaire à React.
* Un rendu conditionnel et des services, semblables à Angular.
* Inspiré par Polymer en partie en termes de simplicité et de performance, Vue offre un style de développement similaire à celui du HTML, des styles et du JavaScript qui sont composés de pair.

Face aux concurrents, Vue a quelques avantages que j'ai apprécié : plus propre, plus d'offres d'API sémantique, une performance légèrement meilleure que React, aucune utilisation de polyfills comme Polymer, une Vue isolée et moins opiniâtre qu'Angular qui est un MVC.

Je pourrais poursuivre, mais il serait préférable que vous lisiez leur comparaison, complète et axée sur la communauté avec d'autres frameworks. Elle vaut la peine d'être lue, mais vous pouvez y revenir plus tard si vous souhaitez plonger dans le code.

## Commençons !

Nous ne pouvons pas commencer sans faire obligatoirement l'exemple "Hello, world!". Faisons-le pour que vous puissiez vous lancer :

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

*[Démo](http://codepen.io/sdras/pen/b52b1252469a353830683aeaccbecd01)*

Si vous êtes familier avec React, vous verrez quelques similitudes. Nous avons sorti vers JavaScript une partie du contenu avec un template de mustache et utilisé une variable, mais l'une des différences, c'est que nous travaillons directement avec du HTML au lieu de [JSX](https://facebook.github.io/react/docs/introducing-jsx.html). JSX est assez facile à travailler, mais je crois vraiment qu'il est agréable de ne pas passer du temps à changer `class` en `className`, etc. Vous remarquerez également que c'est assez léger pour démarrer.

Maintenant, essayons Vue avec quelque chose que j'aime vraiment : les boucles et le rendu conditionnel.

## Rendu conditionnel

Disons que j'ai un ensemble d'éléments, comme la navigation, que je sais que je vais réutiliser. Il serait judicieux de le mettre dans un tableau pour l'actualiser à certains endroits de manière dynamique et cohérente. En vanilla JS (avec Babel), nous pourrions faire quelque chose comme cela : créer le tableau, puis créer une chaîne vide où nous ajoutons chaque élément dans un `<li>`, et envelopper le tout dans un `<ul>` et l'ajouter au DOM avec `innerHTML` :

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

*[Démo](http://codepen.io/sdras/pen/e699f60b79b90a35401cc2bcbc588159)*

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

*[Démo](http://codepen.io/sdras/pen/af6307c633262350c9642f554ff64b55)*

C'est assez propre et déclaratif. Si vous êtes familier avec Angular, cela vous sera probablement familier. Je trouve que c'est une façon propre et lisible de rendre de manière conditionnelle. Si vous deviez sauter dans le code et le mettre à jour, vous pourriez le faire très facilement.

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

*[Démo](http://codepen.io/sdras/pen/fc5a128716814995b888d362a5e1b367)*

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

## Event Handling

Binding that data is all well and good but only gets us so far without event handling, so let's cover that next! This is one of my favorite parts. We'll use the binding and listeners above to listen to DOM events.

There are a few different ways to create usable methods within our application. Just like in vanilla JS, you can pick your function names, but methods are intuitively called, well, methods!

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

We're creating a method called `increment`, and you can see that this automatically binds to `this` and will refer to the data in this instance and component. I love this kind of automatic binding, it's so nice to not have to `console.log` to see what `this` is referring to. We're using shorthand `@click` to bind to the click event here.

Methods aren't the only way to create a custom function. You can also use `watch`. The main difference is that methods are good for small, synchronous calculations, while `watch` is helpful with more tasking or asynchronous or expensive operations in response to changing data. I tend to use watch most often with animations.

Let's go a little further and see how we'd pass in the event itself and do some dynamic style bindings. If you recall in the table above, instead of writing `v-bind`, you can use the shortcut `:`, so we can bind pretty easily to style (as well as other attributes) by using `:style` and passing in state, or `:class`. There are truly a lot of uses for this kind of binding.

In the example below, we're using `hsl()`, in which [hue calculated as a circle of degrees of color](https://css-tricks.com/nerds-guide-color-web/) that wraps all the way around. This is good for our use as it will never fail, so as we track our mouse in pixels across the screen, the background style will update accordingly. We're using [ES6 template literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals) here.

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

*Inclure une image ou codepen*

You can see that we didn't even need to pass in the event to the `@click` handler, Vue will automatically pass it for you to be available as a parameter for the method. (shown as `e` here).

Also, native methods can also be used, such as `event.clientX`, and it's simple to pair them with `this` instances. In the style binding on the element there's camel casing for hyphenated CSS properties. In this example, you can see how simple and declarative Vue is to work with.

We don't even actually need to create a method at all, we could also increase the counter directly inline in the component if the event is simple enough:

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

*Inclure une image ou codepen*

You can see that we're updating the state directly in the `@click` handler without a method at all- you can also see that we can add a little bit of logic in there as well (as you wouldn't have lower than zero items on a shopping site). Once this logic gets too complex, though, you sacrifice legibility, so it's good to move it into a method. It's nice to have the option for either, though.

> Série d'article :
>  1. Rendu, directives et événements (Ce document !)
>  2. Composants, Props et Slots
>  3. Vue-cli
>  4. Vuex
>  5. Animations
