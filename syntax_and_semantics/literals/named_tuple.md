# NamedTuple

Un [tuple nommé](http://crystal-lang.org/api/NamedTuple.html) est typiquement créé avec un litéral de tuple nommé:

```crystal
tuple = {name: "Crystal", year: 2011} # NamedTuple(name: String, year: Int32)
tuple[:name] # => "Crystal" (String)
tuple[:year] # => 2011      (Int32)
```

Pour définir un type de tuple nommé on peut utiliser:

```crystal
# Un type définissant un tuple nommé de x: Int32, y: String
NamedTuple(x: Int32, y: String)
```

Dans les restrictions de type, les arguments de type générique et autres endroits où un type est attendu, vous pouvez utiliser une syntaxe succinte, comme expliqué dans la section [type](../type_grammar.html):

```crystal
# Un tableau de tuples nommés de x: Int32, y: String
Array({x: Int32, y: String})
```

La clef d'un tuple nommé peut également être un litéral de chaîne:

```crystal
{"this is a key": 1}
```
