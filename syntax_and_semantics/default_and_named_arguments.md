# Valeurs par défaut et arguments nommés

Une méthode peut spécifier des valeurs par défaut pour les derniers arguments:

```crystal
class Person
  def become_older(by = 1)
    @age += by
  end
end

john = Person.new "John"
john.age #=> 0

john.become_older
john.age #=> 1

john.become_older 2
john.age #=> 3
```

# Arguments nommés

Tous les arguments peuvent également être spécifiés, en plus de leur position, par leur nom. Par exemple:

```crystal
john.become_older by: 5
```

Quand il y a beaucoup d'arguments, l'ordre des noms dans l'appel n'a pas d'importance,
tant que tous les arguments nécessaires sont donnés:

```crystal
def some_method(x, y = 1, z = 2, w = 3)
  # do something...
end

some_method 10                   # x: 10, y: 1, z: 2, w: 3
some_method 10, z: 10            # x: 10, y: 1, z: 10, w: 3
some_method 10, w: 1, y: 2, z: 3 # x: 10, y: 2, z: 3, w: 1
some_method y: 10, x: 20         # x: 20, y: 10, z: 2, w: 3

some_method y: 10                # Error, missing arugment: x
```

Quand une méthode spécifie un spalt (expliqué dans la section suivante), les arguments nommés ne peuvent être utilisés.
C'est parce-qu'il en devient très difficile de faire la correspondance entre arguments:
il est plus facile d'utiliser les arguments positionnels dans ce cas.
