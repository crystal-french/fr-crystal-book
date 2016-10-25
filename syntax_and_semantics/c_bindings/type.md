# type

Une déclaration `type` dans une `lib` déclare une sorte de `typedef` C, mais plus puissant:

```crystal
lib X
  type MyInt = Int32
end
```

A la différence du C, `Int32` et `MyInt` ne sont pas interchangeables:

```crystal
lib X
  type MyInt = Int32

  fun some_fun(value : MyInt)
end

X.some_fun 1 # Error: argument 'value' of 'X#some_fun'
             # must be X::MyInt, not Int32
```

Ainsi, une déclaration `type` est utile pour des types opaques qui sont créés par la librairie que vous êtes en train d'encapsuler.
Un exemple de ça est le type C `FILE`, que vous pouvez obtenir avec `fopen`.

Reportez-vous à la [grammaire de type](../type_grammar.html) pour la notation utilisée dans les types typedef.
