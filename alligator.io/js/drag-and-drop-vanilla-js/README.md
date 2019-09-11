# Faire du glisser-déposer d'éléments avec du JavaScript et du HTML

>**REMARQUE** : Cet article est une traduction en français de l'excellent article [Drag & Drop Elements with Vanilla JavaScript and HTML](https://alligator.io/js/drag-and-drop-vanilla-js/) écrit par [Jess Mitchell](https://github.com/jessmitch42) avec son aimable autorisation. Tous les articles sur [alligator.io](http://alligator.io) sont la propriété de `alligator.io`.

Il existe de nombreuses bibliothèques JavaScript pour ajouter à votre application une fonction de glisser-déposer. Ce que vous ne savez peut-être pas, c'est que le HTML a une API native intégrée pour permettre le glisser-déposer des éléments du DOM. Ici, nous allons voir comment créer une fonction glisser-déposer à l'aide de l'API HTML « Drag and Drop » avec un peu de JavaScript pour configurer les gestionnaires d'événements.

## Vue d'ensemble

L'API HTML « Drag and Drop » s'appuie sur le modèle d'événement du DOM pour obtenir des informations sur ce qui est glissé ou déposé et pour mettre à jour cet élément par glisser-déposer. Avec seulement quelques gestionnaires d'événements, vous pouvez transformer n'importe quel élément en élément déplaçable ou en zone de déplacement.

L'API « Drag and Drop » offre de multiples options pour personnaliser vos actions au-delà du simple glisser-déposer. Par exemple, vous pouvez mettre à jour le style CSS de vos éléments glissés. En outre, au lieu de simplement déplacer l'élément, vous pouvez choisir de copier votre élément déplaçable pour qu'il soit répliqué sur la zone de déplacement.

Ici, nous allons nous concentrer sur les bases de la construction de vos fonctions JavaScript de glisser-déposer pour mettre à jour directement le DOM.

## Rendre les éléments HTML déplaçables

Commençons d'abord par ce que nous voulons faire glisser. Disons que nous avons un conteneur avec deux types d'éléments enfants : les enfants qui peuvent être déplaçables et les enfants qui peuvent recevoir des éléments déposés. Par exemple, si nous avions une liste de choses à faire, nous pourrions faire glisser nos choses à faire dans la zone « fait ». (Nous reviendrons sur cet exemple de liste de choses à faire à la fin).

Pour simplifier les choses, désignons les éléments déplacés par les éléments `draggable` et la cible par le terme `dropzone`.

```html
<div class='parent'>
  <span id='draggableSpan'>
    draggable
  </span>
  <span> dropzone </span>
</div>
```

Notre premier exemple ici est la structure par défaut et les enfants ne peuvent pas être déplacés.

Commençons donc par rendre explicitement notre élément déplaçable, pour qu'il puisse être glissé. Pour cela, nous devons utiliser l'attribut `draggable` de la manière suivante :

```html
<div class='parent'>
  <span id='draggableSpan' draggable='true'>
    draggable
  </span>
  <span> dropzone </span>
</div>
```

Maintenant, si vous essayez de déplacer l'élément déplaçable avec votre souris (désolé pour les visiteurs mobiles ! 🙈) vous devriez voir une version plus légère de l'élément se déplacer avec votre curseur lors du glissé.

Si cet attribut `draggable` n'est pas défini à `true`, la valeur par défaut est `auto`. Cela signifie que la possibilité de glisser l'élément sera déterminée par les paramètres par défaut de votre navigateur. Par exemple, les liens (`<a>`) sont généralement déplaçables par défaut. Mais les `span` ne le sont pas.

## Création de gestionnaires d’événements « Drag and Drop »

Actuellement, si nous relâchons la souris tout en faisant glisser l'élément déplaçable, rien ne se passe. Ce n'est pas super utile ! Pour déclencher une action sur le glisser-déposer, nous devons utiliser l'API « Drag and Drop » avec au moins trois événements :

- `ondragstart` : définit l'ID de l'élément déplacé et apporte les autres modifications que vous souhaitez appliquer lors du glissement.
- `ondragover` : les navigateurs par défaut ne déclenchent aucune action lorsqu'un élément déplaçable est déposé. Nous devons intervenir ici et attendre que les actions de dépose se produisent !
- `ondragstart` : tout ce qui est supposé se produire lorsque la dépose sera déclenchée. Souvent, l'élément déplaçable se déplacera vers un nouvel élément parent dans le DOM.

> Les gestionnaires d'événements `ondragstart`, `ondragover`, `ondrop` sont là pour débuter. Il y en a actuellement huit au total : `ondrag`, `ondragend`, `ondragenter`, `ondragexit`, `ondragleave`, `ondragover`, `ondragstart` et `ondrop`. 💪

## L'interface DataTransfer

L'interface `DataTransfer` gardera la trace des informations relatives au glissement en cours. Pour mettre à jour notre élément par glisser-déposer, nous devons accéder directement à l'objet `DataTransfer`. Pour ce faire, nous pouvons sélectionner la propriété `dataTransfer` à partir de notre événement DOM.

> L’interface `DataTransfer` (ou l’objet) peut techniquement suivre les informations relatives de plusieurs éléments déplacés en même temps. Pour notre exemple, nous allons nous concentrer sur le glissement d'un seul élément. ✨

## Mise à jour de notre élément lors du glissé

Dans cette prochaine étape, nous allons mettre en place notre fonction `onDragStart`.

Dans notre fonction `ondragstart`, nous pouvons apporter les modifications souhaitées une fois que le glissement a commencé. Vous pouvez mettre à jour le CSS de l'élément glissé, faire de la version glissée une image temporaire et tout ce que vous pouvez imaginer auquel on peut accéder par le biais de l'événement DOM.

La propriété `setData` de l'objet `dataTransfer` peut être utilisée pour définir les informations sur l’état de glissement de l’élément actuellement glissé. Deux paramètres sont nécessaires : une chaîne déclarant le format du deuxième paramètre et les données effectivement transférées.

Notre but est de déplacer notre élément déplaçable vers un nouveau parent. Nous devons donc pouvoir sélectionner notre élément déplaçable avec un ID unique. Nous pouvons définir l'ID de l'élément glissé avec la propriété `setData` afin qu'il puisse être utilisé ultérieurement, comme ceci :

```js
function onDragStart(event) {
  event
    .dataTransfer
    .setData('text/plain', event.target.id);
}
```

Pour mettre à jour le style CSS de l'élément déplacé, nous pouvons accéder à ses styles en utilisant encore l'événement DOM et en définissant les styles souhaités pour le composant `currentTarget` :

```js
function onDragStart(event) {
  event
    .dataTransfer
    .setData('text/plain', event.target.id);

  event
    .currentTarget
    .style
    .backgroundColor = 'yellow';
}
```

> Remarque : si vous souhaitez des styles uniquement pour le glissement, tous les styles que vous modifiez devront être à nouveau mis à jour manuellement lors de la dépose. Donc, si vous changez quelque chose quand il commence à glisser, l'élément déplacé conservera ce nouveau style, sauf si vous le changez à nouveau. 🌈

Maintenant que nous avons notre fonction JavaScript pour le début du glissement, nous pouvons la transmettre à l’attribut `ondragstart` de notre élément déplaçable :

```html
<div class='parent'>
  <span id='draggableSpan'
    draggable='true'
    ondragstart='onDragStart(event);'>
      draggable
  </span>

  <span> dropzone </span>
</div>
```

Si vous n’avez pas la souris pour l’essayer vous-même, voici à quoi ressemblera le glissement.

![Exemple de glissement](https://d33wubrfki0l68.cloudfront.net/6c8a12cc7be91b8682bbfa4c0bbbdfc3161cf7d6/e3d26/images/js/drag-and-drop-vanilla-js/dragnodrop.gif)

Si vous essayez maintenant de faire glisser votre élément, le style déclaré dans `onDragStart` sera appliqué, mais rien ne se passera lors du dépôt. Passons maintenant à nos gestionnaires d’événements pour `dropzone`.

## Permettre aux éléments de recevoir

Après `ondragstart`, notre prochaine fonction à configurer est `ondragover`. Comme mentionné précédemment, le navigateur empêche par défaut les actions de dépose, nous devons donc empêcher le navigateur d'empêcher notre action de dépose. Deux empêchements sont équivalent à une autorisation, non ? 🚀

```js
function onDragOver(event) {
  event.preventDefault();
}
```

Tout ce que nous avons à faire ici est d'empêcher le navigateur d'interférer avec notre action de dépose. Nous pouvons maintenant ajouter cela à notre zone de dépôt (`dropzone`) pour que ce soit un parent accueillant tous les éléments déplaçables.

```html
<div class='parent'>
  <span id='draggableSpan'
    draggable='true'
    ondragstart='onDragStart(event);'>
      draggable
  </span>

  <span ondragover='onDragOver(event);'>
    dropzone
  </span>
</div>
```

Bien que notre zone de dépôt accepte maintenant les éléments déplaçables, nous n'avons toujours pas dit ce qui devrait se passer lorsque la souris est relâchée.

## Que faire en cas de dépose

Nous pouvons maintenant présenter notre troisième et dernière fonction : `ondrop`.

Nos étapes pour cette fonction seront les suivantes :

- Vous vous souvenez des données que nous avons définies avec `setData` ? Maintenant nous devons récupérer ces données avec la propriété `getData` de l'objet `dataTransfer`. La donnée que nous avions définie était l'ID, c'est donc ce qui nous sera retourné.
- Sélectionner notre élément déplaçable avec l'ID que nous avons récupéré à la première étape.
- Sélectionner notre élément `dropzone`.
- Ajouter notre élément déplaçable à la `dropzone`.
- Réinitialiser notre objet `dataTransfer`.

```js
function onDrop(event) {
  const id = event
    .dataTransfer
    .getData('text');

  const draggableElement = document.getElementById(id);
  const dropzone = event.target;

  dropzone.appendChild(draggableElement);

  event
    .dataTransfer
    .clearData();
}
```

Comme il s’agit de la troisième et dernière fonction à construire, il suffit de la passer à l’attribut `ondrop` de `dropzone`. Une fois cela fait, nous avons une fonction glisser-déposer complète !

```html
<div class='parent'>
  <span id='draggableSpan'
    draggable='true'
    ondragstart='onDragStart(event);'>
      draggable
  </span>

  <span
    ondragover='onDragOver(event);'
    ondrop='onDrop(event);'>
      dropzone
  </span>
</div>
```

![Exemple de glisser-déposer](https://d33wubrfki0l68.cloudfront.net/e01c977b1a23b6e01899af98d1176170582f034a/66910/images/js/drag-and-drop-vanilla-js/draganddrop.gif)

Notre exemple, qui est aussi simple que possible, pour montrer comment faire du glisser-déposer sur votre page. Vous pouvez avoir plusieurs éléments déplaçables, plusieurs zones de dépôt et les personnaliser avec tous les autres gestionnaires d'événements de l'API « Drag and Drop ».

Voici un autre exemple d'utilisation de cette API : une simple liste de choses à faire comme mentionnée au début. 🔥

![Exemple de glisser-déposer pour une liste de chose à faire](https://d33wubrfki0l68.cloudfront.net/88008a696406bcd7eb077271911baec22de4887e/5f7ff/images/js/drag-and-drop-vanilla-js/todolist.gif)

Pour reproduire cela, ajoutez simplement d'autres éléments déplaçables exactement comme nous l'avons fait ci-dessus. Assurez-vous simplement que vos ID sont toujours uniques !

## Lectures complémentaires

Pour en savoir plus sur tout ce que vous pouvez déposer avec l’API « Drag and Drop », consultez la [documentation de MDN](https://developer.mozilla.org/fr/docs/Web/API/API_HTML_Drag_and_Drop) à ce sujet.
