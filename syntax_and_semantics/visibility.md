# Visibilité

Les méthodes sont publiques par défaut: le compilateur vous laissera toujours les invoquer.
Parce-que la visibilité publique est le défaut il n'existe pas de mot-clé `public`.

Les méthodes peuvent être marquées comme `private` (**privée**) ou `protected` (**protégée**).

## private

Une méthode `private` peut exclusivement être appelée sans récépteur, autrement dit, sans rien avant le point:

```crystal
class Person
  private def say(message)
    puts message
  end

  def say_hello
    say "hello" # OK, pas de récépteur
    self.say "hello" # Erreur, self est un récépteur

    other = Person.new "Other"
    other.say "hello" # Error, other est un récépteur
  end
end
```

Remarquez que les méthodes `private` sont visibles par les sous-classes:

```crystal
class Employee < Person
  def say_bye
    say "bye" # OK
  end
end
```

## protected

Une méthode `protected` peut seulement être invoquée par:

1. Des instances du même type que le type courant,
2. Des instances du même espace de nom (classe, struct, module, etc.) que le type courant.

```crystal
### Exemple pour 1

class Person
  protected def say(message)
    puts message
  end

  def say_hello
    say "hello" # OK, le self implicite est une Person
    self.say "hello" # OK, self est une Person

    other = Person.new "Other"
    other.say "hello" # OK, other est une Person
  end
end

class Animal
  def make_a_person_talk
    person = Person.new
    person.say "hello" # Erreur, person est une Person
                       # mais le type courant est un Animal
  end
end

one_more = Person.new "One more"
one_more.say "hello" # Erreur, one_more est une Person
                     # mais le type courant est le Programme

### Exemple pour 2

module Namespace
  class Foo
    protected def foo
      puts "Hello"
    end
  end

  class Bar
    def bar
      # Ça marche car Foo et Bar sont sous Namespace
      Foo.new.foo
    end
  end
end

Namespace::Bar.new.bar
```

Une méthode de classe `protected` peut être invoquée depuis une méthode d'instance et inversement:

```crystal
class Person
  protected def self.say(message)
    puts message
  end

  def say_hello
    Person.say "hello" # OK
  end
end
```

## Méthode privée de haut niveau

Ue méthode `private` de haut niveau (top-level) est seulement visible dans le fichier courant.

```crystal
# In file one.cr
private def greet
  puts "Hello"
end

greet #=> "Hello"

# In file two.cr
require "./one"

greet # undefined local variable or method 'greet'
```

Cela vous permet de définir des méthodes assistantes (helpers) qui seront uniquement connues de ce seul fichier.

