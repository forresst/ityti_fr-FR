# TypeScript : Itération sur les objets

![Stefan Baumgartner](https://fettblog.eu/wp-content/uploads/me.jpg)

Publié le 11 mai 2022, écrit par [@ddprrt](https://twitter.com/ddprrt). Temps de lecture : 7 minutes

Il est rare de trouver un casse-tête en TypeScript qui soit plus compliqué que d'essayer d'accéder à la propriété d'un objet en itérant à travers ses clés. Il s'agit d'un modèle qui est si commun en JavaScript, mais TypeScript vous présente tous les problèmes. Avec cette simple ligne :

```typescript
Object.keys(person).map(k => person[k])
```

TypeScript vous balance des gribouillis rouges et les développeurs commencent à s'agacer. Ce n'est tout simplement pas drôle. Il y a plusieurs solutions à cela. J'ai essayé [une première fois « d'améliorer » `Object.keys`](https://fettblog.eu/typescript-better-object-keys/). C'est un bel exercice de fusion de déclarations mais euh... je ne le ferais pas trop souvent. [Dan a également écrit un article très détaillé à ce sujet](https://effectivetypescript.com/2020/05/26/iterate-objects/). L'annotation est certainement une solution.

Mais bon, regardons d'abord le problème.

## Pourquoi itérer des objets n'est pas si facile ?

Jetons un œil à cette fonction :

```typescript
type Person = {
  name: string,
  age: number
}

function printPerson(p: Person) {
  Object.keys(p).forEach((k) => {
      console.log(k, p[k]) // ERREUR !!
  })
}
```

Tout ce que nous voulons, c'est imprimer les champs de `Person` en y accédant par leurs clés. TypeScript ne le permet pas. `Object.keys(p)` retourne un `string[]`, qui est trop étendu pour permettre d'accéder à une forme d'objet bien définie qui est `Person`.

Mais pourquoi en est-il ainsi ? Nous n'accédons qu'aux clés qui sont disponibles, n'est-ce pas logique ? C'est pourtant tout l'intérêt d'utiliser `Object.keys` !

Bien sûr, mais nous avons également la possibilité de transmettre des objets qui sont des sous-types de `Person`, qui ont plus de propriétés que celles définies dans `Person`.

```typescript
const me = {
  name: "Stefan",
  age: 40,
  website: "https://fettblog.eu"
}

printPerson(me); // Tout se passe bien !
```

Donc, vous pourriez me dire que `printPerson` continuera à fonctionner correctement. Il imprime plus de propriétés, ok, mais ça ne casse pas le code. C'est toujours les clés de `p`, donc chaque propriété devrait être accessible.

Bien sûr, mais que se passe-t-il si on n'accède pas à `p` ?

Donc, supposons que `Object.keys` vous donne `(keyof Person)[]`. Comme [mon « truc » de 2 ans essaie de faire](https://fettblog.eu/typescript-better-object-keys/). Vous pouvez facilement écrire quelque chose comme ceci :

```typescript
function printPerson(p: Person) {
  const you: Person = {
    name: "Reader",
    age: NaN
  };

  Object.keys(p).forEach((k) => {
    console.log(k, you[k])
  })  
}

const me = {
  name: "Stefan",
  age: 40,
  website: "https://fettblog.eu"
}

printPerson(me);
```

Si `Object.keys(p)` renvoie un tableau de type `keyof Person[]`, vous pourrez également accéder à d'autres objets de type `Person`. Cela pourrait ne pas coller. Dans notre exemple, nous avons _juste_ imprimé undefined. Mais que se passe-t-il si vous essayez de faire quelque chose avec ces valeurs ? Cela plantera au moment de l'exécution.

TypeScript vous empêche d'avoir des scénarios comme celui-ci. Il est honnête et il vous l'indique : vous pensez que c'est `keyof Person`, mais en réalité, il peut s'agir de beaucoup plus de choses.

Seul le type guards peut vous aider :

```typescript
function isKey<T>(x: T, k: PropertyKey): k is keyof T {
  return k in x
}

function printPerson(p: Person) {
  Object.keys(p).forEach((k) => {
      if(isKey(p, k)) console.log(k, p[k]) // Tout va bien !
  })
}
```

Mais... ce n'est pas parfait, n'est-ce pas ?

## Boucles for-in

Il y a une autre façon d'itérer sur les objets :

```typescript
function printPerson(p: Person) {
  for (let k in p) {
    console.log(k, p[k]) // Erreur
  }
}
```

TypeScript vous donne la même erreur : *L'élément a implicitement un type 'any', car l'expression de type 'string' ne peut pas être utilisée pour indexer le type 'Person'*. C'est la même raison. Vous pouvez aussi faire quelque chose comme ceci :

```typescript
function printPerson(p: Person) {
  const you: Person = {
    name: "Reader",
    age: NaN
  };

  for (let k in p) {
    console.log(k, you[k])
  } 
}

const me = {
  name: "Stefan",
  age: 40,
  website: "https://fettblog.eu"
}

printPerson(me);
```

Et il explosera au moment de l'exécution.

Cependant, en utilisant cet autre moyen, vous avez un petit avantage sur la version `Object.keys`. TypeScript peut être beaucoup plus précis dans ce scénario si vous ajoutez un générique :

```typescript
function printPerson<T extends Person>(p: T) {
  for (let k in p) {
    console.log(k, p[k]) // Ça marche
  }
}
```

Au lieu d'exiger que `p` soit `Person` (et pour permettre d'être compatible avec tous les sous-types de `Person`), nous ajoutons un nouveau paramètre de type générique `T` qui étend `Person`. Cela signifie que tous les types qui ont été compatibles avec cette signature de fonction le sont toujours, mais dès que nous utilisons `p`, nous avons affaire à un sous-type explicite, et non au super-type plus vaste `Person`.

Nous substituons par `T` pour quelque chose qui est compatible avec `Person`, mais où TypeScript sait que c'est suffisamment différent pour vous éviter des erreurs.

Le code ci-dessus fonctionne. `k` est de type `keyof T`. C'est pourquoi nous pouvons accéder à `p`, qui est de type `T`. `T` étant un sous-type de `Person`, ce n'est qu'une coïncidence.

Ainsi nous ne pourrons pas faire des choses qui pourraient planter, comme ça :

```typescript
function printPerson<T extends Person>(p: T) {
  const you: Person = {
    name: "Reader",
    age: NaN
  }
  for (let k in p) {
    console.log(k, you[k]) // ERREUR
  }
}
```

Nous ne pouvons pas accéder à `Person` avec `keyof T`. Ils risquent d'être différents. Magnifique !

Et puisque `T` est un sous-type de `Person`, nous pouvons toujours assigner des propriétés :

```typescript
p.age = you.age
```

Génial !

## Conclusion

Le fait que TypeScript soit très conservateur quant à ses types est quelque chose qui peut sembler étrange au premier abord mais qui vous aide dans des scénarios auxquels vous n'auriez pas pensé. Je suppose que c'est la partie où les développeurs JavaScript hurlent habituellement contre le compilateur et pensent qu'on leur « met des bâtons dans les roues », mais bon, peut-être que TypeScript vous a sauvé la mise. Pour les situations où cela devient agaçant, TypeScript vous donne au moins des moyens de les contourner.
