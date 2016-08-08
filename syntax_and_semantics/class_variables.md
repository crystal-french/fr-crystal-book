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

Les variables de classes sont toujours associées à un seul type et ne peuvent être héritées:

```crystal
class Parent
  @@counter = 0
end

class Child < Parent
  def self.counter
    # Error, can't infer the type of class variable
    # '@@counter' of Child
    @@counter
  end
end
```

Les variables de classe peuvent aussi être associées aux modules et structs.
Comme précédemment, elles ne sont pas héritées en incluant les types.
