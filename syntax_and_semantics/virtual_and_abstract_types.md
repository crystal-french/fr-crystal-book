# Types virtuels et abstraits

Quand le type d'une variable combine différents types de la même hiérarchie de classe,
son type devient un **type virtuel**. Cela s'applique à toute classe et struct sauf
`Reference`, `Value`, `Int` et `Float`. Un exemple:

```crystal
class Animal
end

class Dog < Animal
  def talk
    "Woof!"
  end
end

class Cat < Animal
  def talk
    "Miau"
  end
end

class Person
  getter pet

  def initialize(@name : String, @pet : Animal)
  end
end

john = Person.new "John", Dog.new
peter = Person.new "Peter", Cat.new
```

Si vous compilez le programme précédent avec la commande `tool hierarchy` vous verrez ceci pour `Person`:

```
- class Object
  |
  +- class Reference
     |
     +- class Person
            @name : String
            @pet : Animal+
```

Vous pouvez voir que `@pet` est un `Animal+`. Le `+` signifie que c'est un type virtuel, autrement dit "toute classe hérite de `Animal`, y compris `Animal`".

Le compilateur résoudra toujours une union de type en un type virtuel s'ils sont sous la même hiérarchie:

```
if some_condition
  pet = Dog.new
else
  pet = Cat.new
end

# pet : Animal+
```

Le compilateur fera toujours cela pour les classes et structs sous la même hiérarchie:
il va trouver la première superclasse depuis laquelle tous les types héritent (`Reference`, `Value`, `Int` et `Float` exclues).
S'il ne peut en trouver une, l'union de types est conservée.

La véritable raison pour le compilateur d'agir ainsi est pour être capable de compiler les programmes plus rapidement en évitant de créer plusieurs unions similaires,
et aussi en rendant le code source moins volumieux. Mais, d'un autre côté, c'est raisonné: les classes sous la même hiérarchie devraient
se comporter de la même manière.

Faisons parler l'animal de John:

```crystal
john.pet.talk # Error: undefined method 'talk' for Animal
```

Nous obtenons une erreur car le compilateur traite maintenant `@pet` comme un `Animal+`, ce qui inclut `Animal`.
Et étant donné qu'il ne peut y trouver une méthode `talk`, il retourne une erreur.

Ce que le compilateur ne sait pas comparé à nous, est que `Animal` ne sera jamais instancié car cela n'a aucun sens de l'instancier.
Nous avons un moyen de le dire au compilateur en marquant une classe comme `abstract`:

```crystal
abstract class Animal
end
```

Maintenant le code compile:

```crystal
john.pet.talk #=> "Woof!"
```

Marquer une classe comme abstraite nous empêchera aussi d'en créer une instance:

```crystal
Animal.new # Error: can't instantiate abstract class Animal
```

Pour rendre cela plus explicite qu'un `Animal` doit définir une méthode `talk`,
on peut l'ajouter à `Animal` comme méthode abstraite:

```crystal
abstract class Animal
  # Makes this animal talk
  abstract def talk
end
```

En marquant une méthode comme `abstract` le compilateur va vérifier toutes les sous-classes qui implémentent cette méthode,
même si le programme ne les utilise pas.

Les méthodes abstraites peuvent aussi être définies en modules, et le compilateur va vérifier que les types inclus les implémentent.
