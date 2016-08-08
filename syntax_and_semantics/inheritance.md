# Héritage

Toute classe sauf `Object`, la racine de la hiérarchie, hérite d'une autre classe (sa superclasse).
Si vous n'en spécifiez aucune elle prendra par défaut `Reference` pour les classes et `Struct` pour les structs.

Une classe hérite de toutes les variables d'instance et de toutes les méthodes d'instance et de classe d'une superclasse,
ses constructeurs compris (`new` et `initialize`).

```crystal
class Person
  def initialize(@name : String)
  end

  def greet
    puts "Hi, I'm #{@name}"
  end
end

class Employee < Person
end

employee = Employee.new "John"
employee.greet # "Hi, I'm John"
```

Si une classe définit `new` ou `initialize` alors les contructeurs de sa superclasse ne sont pas hérités:

```crystal
class Person
  def initialize(@name : String)
  end
end

class Employee < Person
  def initialize(@name : String, @company_name : String)
  end
end

Employee.new "John", "Acme" # OK
Employee.new "Peter" # Error: wrong number of arguments
                     # for 'Employee:Class#new' (1 for 2)
```

Vous pouvez passer outre les méthodes dans une classe dérivée:

```crystal
class Person
  def greet(msg)
    puts "Hi, #{msg}"
  end
end

class Employee < Person
  def greet(msg)
    puts "Hello, #{msg}"
  end
end

p = Person.new
p.greet "everyone" # "Hi, everyone"

e = Employee.new
e.greet "everyone" # "Hello, everyone"
```

Plutôt que de passer outre vous pouvez définir des méthodes spécialisées en utilisant des restrictions de type:

```crystal
class Person
  def greet(msg)
    puts "Hi, #{msg}"
  end
end

class Employee < Person
  def greet(msg : Int32)
    puts "Hi, this is a number: #{msg}"
  end
end

e = Employee.new
e.greet "everyone" # "Hi, everyone"

e.greet 1 # "Hi, this is a number: 1"
```

## super

Vous pouvez invoquer la méthode d'une superclasse en utilisant `super`:

```crystal
class Person
  def greet(msg)
    puts "Hello, "#{msg}"
  end
end

class Employee < Person
  def greet(msg)
    super # Same as: super(msg)
    super("another message")
  end
end
```

Sans argument ni parenthèse, `super` reçoit les mêmes arguments que les arguments de la méthode.
Autrement, elle reçoit les arguments que vous lui avez passés.
