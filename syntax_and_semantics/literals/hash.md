# Hash

Un [dictionnaire](http://crystal-lang.org/api/Hash.html) représente une correspondance entre des clés de type `K`
et des valeurs de type `V`. Il est typiquement créé avec un litéral de dictionnaire:

```crystal
{1 => 2, 3 => 4}     # Hash(Int32, Int32)
{1 => 2, 'a' => 3}   # Hash(Int32 | Char, Int32)
```

Un dictionnaire peut mélanger plusieurs types, pour les clés comme pour les valeurs, signifiant que `K`/`V` sera
l'union des types, mais cela est à déterminer à la création du dictionnaire, soit en définissant `K` et `V` soit
en utilisant un litéral de dictionnaire. Dans ce dernier cas, `K` sera défini comme l'union des clefs du dictionnaire,
et `V` sera défini comme l'union des valeurs du dictionnaire.

Quand vous créez un dictionnaire vide vous devez toujours définir `K` et `V`:

```crystal
{} of Int32 => Int32 # same as Hash(Int32, Int32).new
{}                   # syntax error
```

## Types similaires aux dictionnaires

Vous pouvez également utiliser une syntaxe de litéral de dictionnaire spéciale avec d'autres types,
tant que vous définissez une méthode `new` sans argument et une méthode `[]=`:

```crystal
MyType{"foo" => "bar"}
```

Si `MyType` n'est pas générique, l'exemple précédent est équivalent à:

```crystal
tmp = MyType.new
tmp["foo"] = "bar"
tmp
```

Si `MyType` est générique, l'exemple précédent est équivalent à:

```crystal
tmp = MyType(typeof("foo"), typeof("bar")).new
tmp["foo"] = "bar"
tmp
```

Dans le cas d'un type générique, les arguments du type peuvent également être spécifiés:

```crystal
MyType(String, String) {"foo" => "bar"}
```
