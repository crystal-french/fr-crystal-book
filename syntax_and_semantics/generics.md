# Génériques

Les génériques permettent de paramétrer un type basé sur un autre type. Considérons un type Box:

```crystal
class MyBox(T)
  def initialize(@value : T)
  end

  def value
    @value
  end
end

int_box = MyBox(Int32).new(1)
int_box.value # => 1 (Int32)

string_box = MyBox(String).new("hello")
string_box.value # => "hello" (String)

another_box = MyBox(String).new(1) # Error, Int32 doesn't match String
```

Les Génériques sont particulièrement utiles pour implémenter des types de collection. `Array`, `Hash`, `Set` sont des types génériques. `Pointer` également.

Plus d'un argument de type est permis:

```crystal
class MyDictionary(K, V)
end
```

Tout nom peut être utilisé pour les arguments de type:

```crystal
class MyDictionary(KeyType, ValueType)
end
```

## Inférence de variables de type

Les restrictions de type dans un constructeur de type générique sont des variables libres quand les arguments de type ne sont pas spécifiés,
et sont ensuite utilisés pour l'inférence. Par exemple:

```crystal
MyBox.new(1)       # : MyBox(Int32)
MyBox.new("hello") # : MyBox(String)
```

Dans le code précédent nous n'avons pas spécifié les arguments de type de `MyBox`,
le compilateur les a déduits d'après la logique suivante:

* `MyBox.new(value)` délégue à `initialize(@value : T)`,
* `T` n'est pas encore lié à un type, donc le compilateur le lie au type de l'argument donné

De cette manière il est moins laborieux de travailler avec les génériques.

## Structs et modules génériques

Structs et modules peuvent être génériques également. Quand un module est générique vous l'incluez de cette manière:

```crystal
module Moo(T)
  def t
    T
  end
end

class Foo(U)
  include Moo(U)

  def initialize(@value : U)
  end
end

foo = Foo.new(1)
foo.t # Int32
```

Notez que dans l'exmple précédent `T` devient `Int32` car `Foo.new(1)` transforme `U` en `Int32`, qui à son tour transforme `T` en `Int32` via l'inclusion du module générique.

## Héritage de types génériques

Les classes et structs génériques peuvent être hérités. Lors de l'héritage vous pouvez spécifier une instance du type générique,
ou déléguer les variables de type:

```crystal
class Parent(T)
end

class Int32Child < Parent(Int32)
end

class GenericChild(T) < Parent(T)
end
```
