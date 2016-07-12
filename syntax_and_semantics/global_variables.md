# Variables globales

Les variables globales commencent avec un signe dollar (`$`).
Elles sont déclarées lors de la première affectation.

```crystal
$year = 2014
```

Leur type est déduit depuis [l'algorithme d'inférence de type global](type_inference.html).

De plus, si votre programme lit une variable globale avant qu'une valeur lui soit affectée elle aura également le type `Nil`.
