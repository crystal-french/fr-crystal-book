# Opérateurs

Les opérateurs comme `+` et `-` sont des appels ordinaires de méthode. Par exemple:

```crystal
a + b
```

Est équivalent à:

```crystal
a.+(b)
```

Vous pouvez définir un opérateur pour un type comme suit:

```crystal
struct Vector2
  getter x, y

  def initialize(@x : Int32, @y : Int32)
  end

  def +(other)
    Vector2.new(x + other.x, y + other.y)
  end
end

v1 = Vector2.new(1, 2)
v2 = Vector2.new(3, 4)
v1 + v2               #=> Vector2(@x=4, @y=6)
```

Dans la suite se trouve la liste complète des opérateurs avec leurs significations habituelles.

## Opérators unaires

```crystal
+   # positif
-   # négatif
!   # négation
~   # complément bit à bit
```

Ils sont définis sans argument. Par exemple:

```crystal
struct Vector2
  def -
    Vector2.new(-x, -y)
  end
end

v1 = Vector2.new(1, 2)
-v1                    #=> Vector2(@x=-1, @y=-2)
```

**Note:** `!` (négation) ne peut être défini comme méthode (sa signification ne peut être changée).

## Opérateurs binaires

* `+` – addition
* `-` – subtraction
* `*` – multiplication
* `/` – division
* `%` – modulo
* `&` – bitwise and
* `|` – bitwise or
* `^` – bitwise xor
* `**` – exponentiation
* `<<` – shift left, append
* `>>` – shift right
* `==` – equals
* `!=` – not equals
* `<` – less
* `<=` – less or equal
* `>` – greater
* `>=` – greater or equal
* `<=>` – comparison
* `===` – [case equality](case.html)

## Indexage

```crystal
[]  # index d'un array (renvoie l'erreur en dehors des limites)
[]? # index d'un array (nil si en dehors des limites)
[]= # affectation de l'index d'un array
```

Par exemple:

```crystal
class MyArray
  def [](index)
    # ...
  end

  def [](index1, index2, index3)
    # ...
  end

  def []=(index, value)
    # ...
  end
end

array = MyArray.new

array[1]       # invoque la première méthode
array[1, 2, 3] # invoque la seconde méthode
array[1] = 2   # invoque la troisième méthode

array.[](1)       # invoque la première méthode
array.[](1, 2, 3) # invoque la seconde méthode
array.[]=(1, 2)   # invoque la troisième méthode
```

## Signification

On peut assigner toute signification aux opérateurs, mais suivez les conventions précédentes pour éviter que votre code soit difficile à lire, ou qu'il se comporte de manière inattendue.
