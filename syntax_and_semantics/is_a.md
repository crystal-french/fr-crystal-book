# is_a?

La pseudo-méthode `is_a?` détermine si une le type à l'exécution d'une expression hérite ou inclut un autre type.
Par exemple:

```crystal
a = 1
a.is_a?(Int32)          #=> true
a.is_a?(String)         #=> false
a.is_a?(Number)         #=> true
a.is_a?(Int32 | String) #=> true
```

C'est une pseudo-méthode car le compilateur la connaît et peut affecter les information de type,
comme expliqué dans [if var.is_a?(...)](if_varis_a.html). De plus, elle accepte un [type](type_grammar.html)
qui doit être connu à la compilation comme son argument.
