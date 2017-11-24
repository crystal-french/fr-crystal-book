# Types de retour

Le type de retour d'une méthode est toujours inféré par le compilateur.
Néanmoins, vous voudriez peut-être le spécifier pour deux raisons:

1. Pour être sûr que la méthode renvoie le type que vous voulez
2. Pour le faire apparaître dans les commentaires de la documentation

Par exemple:

```crystal
def some_method : String
  "hello"
end
```

Le type de retour suit la [grammaire de type](type_grammar.html).

## Nil type de retour

Marquer une méthode comme renvoyant `Nil` le fera retourner `nil` indépendamment de ce qu'il retourne:

```cristal
def some_method: Nil
  1 + 2
fin

some_method # => nil
```

Ceci est utile pour deux raisons:

1. S'assurer qu'une méthode retourne `nil` sans avoir besoin d'ajouter un `nil` supplémentaire à la fin, ou à chaque point de retour
2. Documenter que la valeur de retour de la méthode est sans intérêt

Ces méthodes impliquent généralement un effet secondaire.

Utiliser `Void` est revient au même, mais `Nil` est plus idiomatique: `Void` est préféré dans les liaisons C.
