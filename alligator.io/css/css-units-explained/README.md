# Les unités CSS expliquées

Le CSS a plusieurs options pour les unités, elles sont utilisées lors de la détermination de la taille de diverses propriétés CSS. L'apprentissage de toutes ces options peut être un élément clé pour gérer facilement le style et qui a fière allure sur n'importe quel écran.

## Qu'est-ce qu'une unité CSS ?

Une unité CSS détermine la taille d'une propriété que vous définissez pour un élément ou son contenu. Par exemple, si vous souhaitez définir la propriété `margin` d'un paragraphe, vous lui attribuez une valeur spécifique. Cette valeur inclut l'unité CSS.

Regardons un petit exemple :

```css
p {
  margin: 20px;
}
```

Dans ce cas, `margin` est la propriété, `20px;` est la valeur et `px` (ou "pixel") est l'unité CSS.

Même s'il est courant de voir des unités comme `px` utilisées, la grande question est souvent de savoir « Quelle est la meilleure unité à utiliser ici ? ».

Eh bien, découvrons-le ! ✨

## Unités absolues par rapport aux unités relatives

Lorsque vous considérez toutes les options pour déterminer quelles sont les unités à utiliser, il est important de considérer les deux catégories d'unités : absolue et relative.

### Unités absolues

Les unités qui sont "absolues" ont la même taille indépendamment de l'élément parent ou de la taille de la fenêtre. Cela signifie que la définition de propriétés avec une valeur qui a une unité absolue sera de cette taille lorsqu'on la regarde sur un téléphone ou sur un grand écran (et tout ce qui se trouve entre les deux !).

Les unités absolues peuvent être utiles pour travailler sur un projet où la réactivité n'est pas prise en compte. Par exemple, les applications de bureau qui ne peuvent pas être redimensionnées, peuvent être stylisées pour des dimensions par défaut. Si la fenêtre ne change pas d'échelle, vous n'avez pas besoin non plus du contenu.

> Conseil : les unités absolues peuvent être moins favorables pour les sites réactifs parce qu'elles n'évoluent pas lorsque la taille de l'écran change.

Absolue | Description unité | Exemple
---------|-----------------|--------
px | 1/96 de 1 pouce (96px = 1 pouce) | font-size: 12px;
pt | 1/72 of 1 pouce (72pt = 1 pouce) | font-size: 12pt;
pc | 12pt = 1pc | font-size: 1.2pc;
cm | centimétre | font-size: 0.6cm;
mm | millimétre | (10 mm = 1 cm) |	font-size: 4mm;
in | pouce (inch) |	font-size: 0.2in;
