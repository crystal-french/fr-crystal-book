# Tuple

Un [Tuple](http://crystal-lang.org/api/Tuple.html) est typiquement créé avec un litéral de tuple:

```crystal
tuple = {1, "hello", 'x'} # Tuple(Int32, String, Char)
tuple[0]                  #=> 1       (Int32)
tuple[1]                  #=> "hello" (String)
tuple[2]                  #=> 'x'     (Char)
```

Pour créer un tuple vide on utilise [Tuple.new](http://crystal-lang.org/api/Tuple.html#new%28%2Aargs%29-class-method).

Pour définir un type de tuple vous pouvez utiliser:

```crystal
# Un type définissant un tuple de Int32, String et Char
Tuple(Int32, String, Char)
```

Dans les restrictions de type, les arguments de type générique et autres endroits où un type est attendu, vous pouvez utiliser une syntaxe succinte, comme expliqué dans la section [type](../type_grammar.html):

```crystal
# Un tableau de tuples de Int32, String et Char
Array({Int32, String, Char})
```
