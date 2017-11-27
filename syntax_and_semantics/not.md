# if !

L'opérateur `!` retourne un `Bool` qui résulte de la négation de la [vérité](truthy_and_falsey_values.html) d'une valeur.

Quand c'est utilisé dans un `if` en conjontion avec une variable, `is_a?`, `responds_to?` ou `nil?`
le compilateur restreindra les types en conséquence:

```crystal
a = une_condition ? nil : 3
if !a
  # ici a est Nil parce-que a est faux dans cette branche
else
  # ici a est Int32, parce-que a est vraie dans cette branche
end
```

```crystal
b = une_condition ? 1 : "x"
if !b.is_a?(Int32)
  # ici b est String parce-que ce n'est pas un Int32
end
```
