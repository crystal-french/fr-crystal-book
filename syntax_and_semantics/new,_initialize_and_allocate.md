# new, initialize et allocate

Vous créez une instance d'une classe en appelant `new` sur cette classe:

```
person = Person.new
```

Ici, `person` est une instance de `Person`.

On ne peut pas faire grand chose avec`person`, alors ajoutons-y quelques concepts.
Une `Person` a un nom et un âge. Dans la section "tout est objet" nous avons vu qu'un objet a un type et répond à des méthodes,
seul moyen d'interagir avec des objets, nous aurons donc besoin d'un méthode `name` et `age`.
Nous stockerons ces informations dans des variables d'instance, qui sont toujours préfixées d'un `@`.
Nous voulons également qu'une `Person` débute son existence avec un nom de notre choix et un âge égal à zéro.
On code la partie "débuter son existence" avec une méthode spéciale `initialize`,
qui est habituellement appelée un *constructeur*:

```crystal
class Person
  def initialize(name : String)
    @name = name
    @age = 0
  end

  def name
    @name
  end

  def age
    @age
  end
end
```

Maintenant nous pouvons créer des personnes comme suit:

```crystal
john = Person.new "John"
peter = Person.new "Peter"

john.name #=> "John"
john.age #=> 0

peter.name #=> "Peter"
```

(Si vous vous demandez pourquoi nous avons eu à spécifier que `name` est une `String` mais rien pour `age`, lisez [l'algorithme d'inférence de type globale](type_inference.html))

Remarquez que nous créons une `Person` avec `new` mais nous avons défini l'initialisation dans une méthode `initialize`,
pas dans une méthode `new`. Pourquoi?

La réponse est que lorsque nous avons défini une méthode `initialize` Crystal a défini une méthode `new` pour nous, comme cela:

```crystal
class Person
  def self.new(name : String)
    instance = Person.allocate
    instance.initialize(name)
    instance
  end
end
```

D'abord, remarquez la notation `self.new`. Elle signifie que la méthode appartient à la **classe** `Person`, pas à une instance en particulier de cette classe.
C'est pour cela que l'on peut faire `Person.new`.

Ensuite, `allocate` est une classe bas-niveau de méthode qui crée un objet non initialisé du type donné.
Elle alloue simplement la mémoire nécessaire à celui-ci. Ensuite `initialize` est appelée dessus et puis vous avez l'instance.
Vous n'avez généralement pas à invoquer `allocate`, car elle n'est [pas sûre](unsafe.html), mais c'est pour cela que `new` et `initialize` sont liées.
