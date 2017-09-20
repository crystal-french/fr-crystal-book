# union

Une déclaration `union` dans une `lib` déclare une union C:

```crystal
lib U
  # In C:
  #
  #  union IntOrFloat {
  #    int some_int;
  #    double some_float;
  #  };
  union IntOrFloat
    some_int : Int32
    some_float : Float64
  end
end
```

Pour créer une instance d'une union on utilise `new`:

```crystal
value = U::IntOrFloat.new
```

Cela alloue une union sur la pile.

Une union C démarre avec tous ses champs à "zéro": entiers et flottants démarrent à zéro, pointeurs démarrent avec une adresse à zéro, etc.

Pour éviter cette initialisation vous pouvez utiliser `uninitialized`:

```crystal
value = uninitialized U::IntOrFloat
value.some_int #=> some garbage value
```

Vous pouvez définir et accéder à ses propriétés:

```crystal
value = U::IntOrFloat.new
value.some_int = 1
value.some_int #=> 1
value.some_float #=> 4.94066e-324
```

Si la valeur affectée n'est pas exactement celle du type de la propriété, [to_unsafe](to_unsafe.html) sera essayée.

Une union C est passée par valeur (par copie) aux fonctions et méthodes, et aussi passée par valeur quand elle est retournée d'une méthode:

```crystal
def change_it(value)
  value.some_int = 1
end

value = U::IntOrFloat.new
change_it value
value.some_int #=> 0
```

Reportez-vous à la [grammaire de type](../type_grammar.html) pour la notation utilisée dans les types des champs de l'union.
