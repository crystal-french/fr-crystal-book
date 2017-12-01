# Méthodes et variables d'instance

Nous pouvons simplifier notre constructeur en utilisant une syntaxe plus succinte pour affecter un argument de méthode à une variable d'instance:

```crystal
class Person
  def initialize(@name : String)
    @age = 0
  end

  def age
    @age
  end
end
```

Pour le moment, nous ne pouvons faire grand chose avec une personne, a part la créer avec un nom. Son âge, qui sera toujours égal à zéro. Alors ajoutons une méthode afin de pouvoir vieillir une personne:

```crystal
class Person
  def initialize(@name : String)
    @age = 0
  end

  def age
    @age
  end

  def become_older
    @age += 1
  end
end

john = Person.new "John"
peter = Person.new "Peter"

john.age #=> 0

john.become_older
john.age #=> 1

peter.age #=> 0
```

Les noms de méthode commencent avec une lettre minuscule, et, par convention, contiennent seulement des lettres minuscules, des soulignés et des nombres.

De plus, on peut définir `become_older` dans la définition originale de `Person`, ou dans une autre définition:
Crystal combine toutes les définitions en une classe unique. Ce qui suit est tout à fait valide:

```crystal
class Person
  def initialize(@name : String)
    @age = 0
  end
end

class Person
  def become_older
    @age += 1
  end
end
```

## Redéfinir des méthodes, et previous_def

Si vous redéfinissez une méthode, la dernière définition prime.

```crystal
class Person
  def become_older
    @age += 1
  end
end

class Person
  def become_older
    @age += 2
  end
end

person = Person.new "John"
person.become_older
person.age #=> 2
```

Vous pouvez invoquer la méthode précédemment redéfinie avec `previous_def`:

```crystal
class Person
  def become_older
    @age += 1
  end
end

class Person
  def become_older
    previous_def
    @age += 2
  end
end

person = Person.new "John"
person.become_older
person.age #=> 3
```

Sans arguments ni parenthèse, `previous_def` reçoit les mêmes arguments que les arguments de la méthode.
Sinon, elle reçoit les arguments que vous lui passez.

## Initialisation catch-all

Les variables d'instance peuvent aussi être initialisées hors des méthodes `initialize`:

```crystal
class Person
  @age = 0

  def initialize(@name : String)
  end
end
```

Cela va initialiser `@age` à zero dans chaque constructeur. C'est utile pour éviter la déduplication, mais aussi pour éviter le type `Nil` à la ré-ouverture d'une classe pour lui ajouter des variables d'instance.
