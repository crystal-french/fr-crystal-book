# if var

Si une variable est la condition d'un `if`,
dans la branche `then` la variable sera considérée comme n'ayant pas le type `Nil`:

```crystal
a = une_condition ? nil : 3
# a est Int32 ou Nil

if a
  # Comme le seul moyen d'arriver ici est si a est vraie,
  # a ne peut être nulle. Alors a est Int32.
  a.abs
end
```

Cela s'applique également au cas où une variable est affectée dans la condition d'un `if`:

```crystal
if a = une_expression
  # ici a est non nulle
end
```

Cette logique s'applique également s'il y a un ET logique (`&&`) dans la condition:

```crystal
if a && b
  # ici a comme b sont assurées de ne pas être Nil
end
```

Ici, la partie droite de l'expressions `&&` est aussi garantie pour avoir `a` comme non `Nil`.

Bien sûr, ré-affecter une variable dans une branche `then`
fait que cette variable a un nouveau type basé sur l'affectation de l'expression.

La logique précédente ne fonctionne **pas** avec les variables d'instance, les variables de classe
ou les variables globales:

```crystal
if @a
  # ici @a peut être nulle
end
```

Tout ça parce que tout appel de méthode peut potentiellement impacter cette variable d'instance,
la rendant `nil`. Une autre raison est qu'un autre thread peut changer cette variable d'instance après le test de la condition.

Pour faire quelque chose avec `@a` seulement quand elle n'est pas `nil` vous avez deux choix:

```crystal
# Première possibilité: l'affecter à une variable
if a = @a
  # ici a ne peut être nulle
end

# Seconde possibilité: utiliser `Object#try` qui se trouve dans la librairie standard
@a.try do |a|
  # ici a ne peut être nulle
end
```

Cette logique ne fonctionne pas sur les appels de méthode et proc, accesseurs et propriétés inclus,
parce que les méthodes et procs(ou, plus généralement, tout union de types) pouvant être nuls
n'offrent aucune garantie de retourner le même type spécifique entre deux appels successifs.

```crystal
if méthode # premier appel à une méthode qui peut renvoyer Int32 ou Nil
           # ici nous savons que le premier appel n'a pas retourné Nil
  méthode  # le second appel peut toujours retourner Int32 ou Nil
end
```

Les techniques précédentes pour les variables d'instance peuvent aussi fonctionner sur les appels de méthode et proc.
