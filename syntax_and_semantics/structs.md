# Structs

Au lieu de définir un type à l'aide de `class` vous pouvez le faire à l'aide de `struct`:

```crystal
struct Point
  property x, y

  def initialize(@x : Int32, @y : Int32)
  end
end
```

La différence entre une struct et un classe:
* Invoquer `new` sur une struct crée une allocation sur la pile et non sur le tas,
* Une struct est [passée par valeur](http://crystal-lang.org/api/Value.html) alors qu'une classe est passée par référence,
* Une struct hérite implicitement de [Struct](http://crystal-lang.org/api/Struct.html), qui hérite de [Value](http://crystal-lang.org/api/Value.html).
  Une classe hérite implicitement de [Reference](http://crystal-lang.org/api/Reference.html).
* Une struct ne peut hériter d'une struct non-abstraite.

Le dernier point a une raison: une struct a une mise en page mémoire bien définie.
Par exemple, la struct `Point` précédente occupe 8 bytes.
Si vous avez un tableau de points, les points sont inclus dans le tampon du tableau:

```crystal
# The array's buffer will have each 8 bytes dedicated to each Point
ary = [] of Point
```

Si `Point` est hérité, un tableau d'un tel type doit également prendre en compte le fait que d'autres types peuvent y être inclus,
la taille de chaque élément doit donc croître en conséquence. Ce qui est complétement imprévisible. Ainsi, les structs non-abstraites ne peuvent être héritées.
Les structs abstraites, d'un autre côté, auront des descendants, il est donc prévisible qu'un tel tableau prenne en compte la possibilité d'inclure plusieurs types.

Une struct peut aussi inclure des modules et être générique, tout comme une classe.

Une struct est surtout utilisée pour des raisons de performance pour éviter de faire de multiples petites allocations lorsque le passage
de petites copies serait plus efficace.

Alors comment choisir entre une struct et une classe? La règle de base est que si aucune variable d'instance ne sera jamais réassignée, autrement dit votre type est immuable,
vous pouvez utiliser une struct, sinon utilisez une classe.
