# Surcharge

On peut définir une méthode `become_older` qui accepte comme argument le nombre d'années à rendre plus vieux une personne:

```crystal
class Person
  @age = 0

  def become_older
    @age += 1
  end

  def become_older(years)
    @age += years
  end
end

john = Person.new "John"
john.age #=> 0

john.become_older
john.age #=> 1

john.become_older 5
john.age #=> 6
```

Ainsi, vous pouvez avoir différentes méthodes avec le même nom et un nombre différent d'arguments et elles seront
considérées comme des méthodes différentes. On appelle cela la *surcharge de méthode*.

On surcharge des méthodes suivant différents critères:

* Le nombre d'arguments,
* Les restrictions de type appliquées aux arguments,
* Les noms des arguments nommés requis,
* Si la méthode accepte un [bloc](blocks_and_procs.html) ou non.

Par exemple, on peut définir quatre méthodes `become_older` différentes:

```crystal
class Person
  @age = 0

  # Incrémente l'âge de un an
  def become_older
    @age += 1
  end


  # Incrémente l'âge du nombre d'années donné
  def become_older(years : Int32)
    @age += years
  end

  # Incrémente l'âge du nombre d'années donné, au format String
  def become_older(years : String)
    @age += years.to_i
  end

  # Utilise yield pour renvoyer l'âge courant de la personne et
  # incrémenter son âge par la valeur renvoyée par le bloc.
  def become_older
    @age += yield @age
  end
end

person = Person.new "John"

person.become_older
person.age #=> 1

person.become_older 5
person.age #=> 6

person.become_older "12"
person.age #=> 18

person.become_older do |current_age|
  current_age < 20 ? 10 : 30
end
person.age #=> 28
```

Remarquez que dans le cas de la méthode utilisant yield, le compilateur s'en rend compte à cause de l'expression `yield`.
Pour que ce soit encore plus explicite, vous pouvez ajouter argument de bloc `&block` à la fin:

```crystal
class Person
  @age = 0

  def become_older(&block)
    @age += yield @age
  end
end
```

Dans la documentation générée la méthode `&block` sera toujours affichée, que vous l'ayez écrite ou non.

Pour un nombre d'arguments donné, le compilateur essaiera de les trier en laissant le moins restrictif à la fin:

```crystal
class Person
  @age = 0

  # D'abord, on définit cette méthode
  def become_older(age)
    @age += age
  end

  # Etant donné que "String" est plus restrictive que pas de restriction
  # du tout, le compilateur place cette méthode avant la précédente
  # lorsqu'il prend en considération quelle surcharge correspond.
  def become_older(age : String)
    @age += age.to_i
  end
end

person = Person.new "John"

# Invokes the first definition
person.become_older 20

# Invokes the second definition
person.become_older "12"
```

Néanmoins, le compilateur peut ne pas toujours se rendre compte de l'ordre car il n'y a pas toujours d'ordre strict,
il est donc conseillé de toujours laisser les méthodes restrictives à la fin.
