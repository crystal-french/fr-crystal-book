# Modules

L'utilisation de modules a deux objectifs:

* comme espace de nom pour définir d'autres types, méthodes et constantes,
* comme types partiels qui peuvent être inclus dans d'autres types.

Un exemple de module comme espace de nom:

```crystal
module Curses
  class Window
  end
end

Curses::Window.new
```

Les auteurs de librairies sont encouragés à mettre leurs définitions dans des modules pour éviter les collisions de nommage.
La librairie standard n'a généralement pas d'espace de nom étant donné que ses types et méthodes sont très communs, afin d'éviter d'écrire des noms longs.

Pour utiliser un module comme type partiel on utilise `include` ou `extend`.

Un `include` fait qu'un type inclut des méthodes définies dans un module comme méthodes d'instance:

```crystal
module ItemsSize
  def size
    items.size
  end
end

class Items
  include ItemsSize

  def items
    [1, 2, 3]
  end
end

items = Items.new
items.size #=> 3
```

Dans l'espace précédent, c'est comme si l'on avait copié-collé la méthode `size` du module dans la classe `Items`.
Cela marche en réalité du fait que chaque type a une liste d'ancêtres, ou parents. Par défaut cette liste débute avec la super classe.
Au fur et à mesure de leur inclusion, les modules sont ajoutés en tête de cette liste. Quand une méthode n'est pas trouvée dans un type, elle est recherchée dans cette liste. À l'invocation de `super`, le premier type de cette liste d'ancêtres est utilisé.

Un `module` peut inclure d'autres modules, ainsi si une méthode n'est pas trouvée dans un module elle sera recherchée dans les modules inclus.

Un `extend` fait qu'un type inclut des méthodes définies dans un module comme méthodes de classe:

```crystal
module SomeSize
  def size
    3
  end
end

class Items
  extend SomeSize
end

Items.size #=> 3
```

`include` comme `extend` rendent les constantes définies dans un module disponible au type inclus/étendu.

Les deux peuvent être utilisées en tête, pour éviter d'avoir à écrire un espace de nom encore et encore (bien que les chances de collision de nommage augmentent):

```crystal
module SomeModule
  class SomeType
  end

  def some_method
    1
  end
end

include SomeModule

SomeType.new # OK, same as SomeModule::SomeType
some_method  # OK, 1
```

## extend self

Un modèle commun des modules est `extend self`:

```crystal
module Base64
  extend self

  def encode64(string)
    # ...
  end

  def decode64(string)
    # ...
  end
end
```

De cette manière un module peut être utilisé comme espace de nom:

```crystal
Base64.encode64 "hello" #=> "aGVsbG8="
```

Mais il peut aussi être inclus dans le programme et ses méthodes peuvent être invoquées sans espace de nom:

```crystal
include Base64

encode64 "hello" #=> "aGVsbG8="
```

Pour que cela soit utile le nom de la méthode doit présenter un lien avec le module,
sinon les chances de collision de nom sont élevées.

Un module ne peut être instancié:

```crystal
module Moo
end

Moo.new # undefined method 'new' for Moo:Class
```
