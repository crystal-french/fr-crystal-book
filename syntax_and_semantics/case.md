# case

Un `case` est une expression qui permet une sorte de correspondance par motif.
Elle permet d'écrire une chaîne de if-else-if avec une légère différence sémantique
et quelques structures puissantes supplémentaires.

Dans sa forme basique, elle permet de faire correspondre une valeur avec d'autres valeurs:

```crystal
case exp
when valeur1, valeur2
  faire_quelque_chose
when value3
  faire_quelque_chose_autre
else
  faire_encore_autre_chose
end

# L'exemple précédent est équivalent à:
tmp = exp
if value1 === tmp || value2 === tmp
  faire_quelque_chose
elsif value3 === tmp
  faire_encore_autre_chose
else
  faire_encore_autre_chose
end
```

Remarquez que `===` est utilisé pour comparer une expression par rapport à une valeur d'un `case`.

Si l'expression d'un `when` est un type, `is_a?` est utilisé.
De plus, si l'expression du case est une variable ou une affectation de variable le type de cette variable est restreint:

```crystal
case var
when String
  # var : String
  faire_quelque_chose
when Int32
  # var : Int32
  faire_encore_autre_chose
else
  # ici var est ni une String ni un Int32
  faire_encore_autre_chose
end

# L'exemple précédent est équivalent à:
if var.is_a?(String)
  faire_quelque_chose
elsif var.is_a?(Int32)
  faire_encore_autre_chose
else
  faire_encore_autre_chose
end
```

Vous pouvez invoquer une méthode comme expression du `case` dans un `when`
en utilisant la syntaxe d'objet implicite:

```crystal
case num
when .even?
  faire_quelque_chose
when .odd?
  faire_encore_autre_chose
end

# L'exemple précédent est équivalent à:
tmp = num
if tmp.even?
  faire_quelque_chose
elsif tmp.odd?
  faire_encore_autre_chose
end
```

Enfin, vous pouvez omettre la valeur du `case`:

```crystal
case
when cond1, cond2
  faire_quelque_chose
when cond3
  faire_encore_autre_chose
end

# L'exemple précédent est équivalent à:
if cond1 || cond2
  faire_quelque_chose
elsif cond3
  faire_encore_autre_chose
end
```

Cela permet parfois d'écrire du code plus naturel à lire.

## Litéral de tuple

Quand l'expression d'un case est un litéral de tuple il y a quelques différences sémantiques si la condition
du when est aussi un litéral de tuple.

### La taille du tuple doit correspondre

```crystal
case {value1, value2}
when {0, 0} # OK, 2 éléments
  # ...
when {1, 2, 3} # Erreur du compilateur, correspondance impossible
  # ...
end
```

### Souligné autorisé

```crystal
case {value1, value2}
when {0, _}
  # Correspondance si 0 === value1, aucun test sur value2
when {_, 0}
  # Correspondance si 0 === value2, aucun test sur value1
end
```

### Objet implicit autorisé

```crystal
case {value1, value2}
when {.even?, .odd?}
  # Correspondance si value1.even? && value2.odd?
end
```

### Une comparaison sur un type va exécuter une vérification avec is_a?

```crystal
case {value1, value2}
when {String, Int32}
  # Correspondance si value1.is_a?(String) && value2.is_a?(Int32)
  # Le type de value1 est connu comme étant String par le compilateur,
  # et le type de value2 comme étant Int32
end
```
