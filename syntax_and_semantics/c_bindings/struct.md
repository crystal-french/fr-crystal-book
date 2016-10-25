# struct

Une déclaration `struct` dans une `lib` déclare une struct C.

```crystal
lib C
  # En C:
  #
  #  struct TimeZone {
  #    int minutes_west;
  #    int dst_time;
  #  };
  struct TimeZone
    minutes_west : Int32
    dst_time     : Int32
  end
end
```

Vous pouvez aussi spécifier plusieurs champs de même type:

```crystal
lib C
  struct TimeZone
    minutes_west, dst_time : Int32
  end
end
```

Les structs récursives ont un fonctionnement sans surprise:

```crystal
lib C
  struct LinkedListNode
    prev, _next : LinkedListNode*
  end

  struct LinkedList
    head : LinkedListNode*
  end
end
```

Pour créer une instance d'une struct utilisez `new`:

```crystal
tz = C::TimeZone.new
```

Cela alloue la struct sur la pile.

Une struct C démarre avec tous ses champs à "zéro": entiers et flottants démarrent à zéro, pointeurs démarrent avec une adresse à zéro, etc.

Pour éviter cette initialisation vous pouvez utiliser `uninitialized`:

```crystal
tz = uninitialized C::TimeZone
tz.minutes_west #=> some garbage value
```

Vous pouvez modifier et accéder à ces propriétés:

```crystal
tz = C::TimeZone.new
tz.minutes_west = 1
tz.minutes_west #=> 1
```

Si la valeur affectée n'est pas exactement la même que le type de la propriété, [to_unsafe](to_unsafe.html) sera essayé.

Vous pouvez aussi initialiser certains champs avec une syntaxe similaire aux [arguments nommés](../default_and_named_arguments.html):

```crystal
tz = C::TimeZone.new minutes_west: 1, dst_time: 2
tz.minutes_west #=> 1
tz.dst_time     #=> 2
```

Une struct C est passée par valeur (en tant que copie) aux fonctions et méthodes,
et aussi passée par valeur quand retournée par méthode:

```crystal
def change_it(tz)
  tz.minutes_west = 1
end

tz = C::TimeZone.new
change_it tz
tz.minutes_west #=> 0
```

Reportez-vous à la [grammaire de type](../type_grammar.html) pour la notation utilisée dans les types de champs de struct.
