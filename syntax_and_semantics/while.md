# while

Un `while` exécute son corps tant que sa condition est *vraie*.

```crystal
while une_condition
  faire_cela
end
```

On commence par tester la condition, et si elle est *vraie*, le corps est exécuté.
Ainsi, le corps peut ne jamais être exécuté.

Le type du `while` est toujours `Nil`.

Similaire à un `if`, si la condition d'un `while` est une variable,
la variable est garantie pour ne pas être `nil` dans le corps.
Si la condition est un test `var.is_a?(Type)`, `var` est garantie pour être de type Type dans le corps.
Et si la condition est  `var.responds_to?(:method)`, `var` est garanti d'être d'un type qui répond à cette méthode.

Le type d'une variable après un `while` dépend du type qu'elle avait avant le `while`
et du type qu'elle avait avant de quitter le corps du `while`:

```crystal
a = 1
while une_condition
  # a : Int32 | String
  a = "hello"
  # a : String
  a.size
end
# a : Int32 | String
```

## Vérifier la condition avant la fin d'une boucle (loop)

Si vous avez besoin d'exécuter le corps au moins une fois avant de vérifier une condition de sortie,
vous pouvez faire:

```crystal
while true
  faire_quelque_chose
  break if une_condition
end
```

Ou utiliser `loop`, disponible depuis la librairie standard:

```crystal
loop do
  faire_quelque_chose
  break if une_condition
end
```
