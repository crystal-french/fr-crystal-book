# Variables

Les variables exposées par une librairie C peuvent être déclarées
dans une déclaration `lib` en utilisant une déclaration à la manière d'une variable globale:

```crystal
lib C
  $errno : Int32
end
```

Ensuite elle peut être accédée et modifiée:

```crystal
C.errno #=> some value
C.errno = 0
C.errno #=> 0
```

Une variable peut être marquée comme thread local avec un attribut:

```crystal
lib C
  @[ThreadLocal]
  $errno : Int32
end
```

Rapportez-vous à la [grammaire de type](../type_grammar.html) pour la notation utilisée dans les types de variables externes.
