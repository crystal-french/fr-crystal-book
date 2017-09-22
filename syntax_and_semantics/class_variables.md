# Variables de classe

Les variables de classe sont associées aux classes plutôt qu'aux instances.
Elles sont préfixées par deux arobases (`@@`). Par exemple:

```crystal
class Counter
  @@instances = 0

  def initialize
    @@instances += 1
  end

  def self.instances
    @@instances
  end
end

Counter.instances #=> 0
Counter.new
Counter.new
Counter.new
Counter.instances #=> 3
```

Les variables de classe peuvent être accédées ou modifiées depuis les méthodes de classe d'instance.

Leur type est inféré suivant [l'algorithme d'inférence de type global](type_inference.html).

Les variables de classe sont héritées par des sous-classes avec une condition: leur type est le même, mais chaque classe possède une valeur d'exécution différente. Par exemple:

```crystal
class Parent
  @@numbers = [] of Int32

  def self.numbers
    @@numbers
  end
end

class Child < Parent
end

Parent.numbers # => []
Child.numbers # => []

Parent.numbers << 1
Parent.numbers # => [1]
Child.numbers # => []
```

Les variables de classe peuvent aussi être associées aux modules et structs.
Comme précédemment, elles ne sont pas héritées en incluant les types.
