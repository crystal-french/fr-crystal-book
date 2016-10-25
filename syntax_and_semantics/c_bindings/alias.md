# alias

Une déclaration `alias` dans une `lib` déclare un `typedef` C:

```crystal
lib X
  alias MyInt = Int32
end
```

Désormais `Int32` et `MyInt` sont interchangeables:

```crystal
lib X
  alias MyInt = Int32

  fun some_fun(value : MyInt)
end

X.some_fun 1 # OK
```

Un `alias` est surtout utile pour éviter d'écrire de longs types encore et encore,
mais aussi pour déclarer un type basé sur des drapeaux de compilation:

```crystal
lib C
  {% if flag?:(x86_64) %}
    alias SizeT = Int64
  {% else %}
    alias SizeT = Int32
  {% end %}

  fun memcmp(p1 : Void*, p2 : Void*, size : C::SizeT) : Int32
end
```

Reportez-vous à la [grammaire de type](../type_grammar.html) pour la notation utilisée dans les types alias.
