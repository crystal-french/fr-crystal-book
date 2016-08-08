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
