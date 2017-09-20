# Array

Un [Tableau](http://crystal-lang.org/api/Array.html) est un type générique pour contenir des éléments de type `T`.
Il est typiquement créé à l'aide d'un litéral de tableau:

```crystal
[1, 2, 3]         # Array(Int32)
[1, "hello", 'x'] # Array(Int32 | String | Char)
```

Un tableau peut mélanger des types, ce qui signifie que `T` sera l'union de plusieurs types, mais c'est à déterminer à la création du tableau, soit en spécifiant T soit en utilisant un litéral de tableau.
Dans ce dernier cas, T sera défini comme l'union des éléments utilisés dans le litéral de tableau.

Lors de la création d'un tableau vide vous devez toujours spécifier T:

```crystal
[] of Int32 # same as Array(Int32).new
[]          # syntax error
```

## Tableau de Chaîne

Des tableaux de chaînes peuvent être créés avec une syntaxe spéciale:

```crystal
%w(one two three) # ["one", "two", "three"]
```

## Tableau de Symbôle

Des tableaux de symbôles peuvent être créés avec une syntaxe spéciale:

```crystal
%i(one two three) # [:one, :two, :three]
```

## Types semblables au tableau

Vous pouvez utiliser une syntaxe de tableau spéciale via d'autres types, tant qu'ils définissent une méthode `new` sans argument et une méthode `<<`:

```crystal
MyType{1, 2, 3}
```

Si `MyType` n'est pas générique, l'exemple précédent est équivalent à:

```crystal
tmp = MyType.new
tmp << 1
tmp << 2
tmp << 3
tmp
```

Si `MyType` est générique, l'exemple précédent est équivalent à:

```crystal
tmp = MyType(typeof(1, 2, 3)).new
tmp << 1
tmp << 2
tmp << 3
tmp
```

Dans le cas d'un type générique, les arguments du type peuvent également être spécifiés:

```crystal
MyType(Int32 | String) {1, 2, "foo"}
```
