# Inférence de type

La philosophie de Crystal est de nécessiter aussi peu d'annotations que possible.
Néanmoins, certaines annotations sont nécessaires.

Considérons la définition de classe suivante:

```crystal
class Person
  def initialize(@name)
    @age = 0
  end
end
```

On se rend rapidement compte que `@age` est un entier, mais nous ne connaissons pas le type de `@name`.
Le compilateur peut inférer ce type à partir des utilisations de la classe `Person`.
Néanmoins, ceci présente quelques problèmes:

* Le type n'est pas évident pour quelqu'un à la lecture du code: il lui faudrait vérifier les utilisations
  de la classe `Person` pour le découvrir,
* Certaines optimisations du compilateur, comme analyser qu'une fois une méthode, et la compilation incrémentale,
  sont quasi-impossible.

Lorsque que l'ensemble d'un code source s'accroit, ces problèmes prennent encore plus d'ampleur:
comprendre un projet devient plus difficile, et les temps de compilation deviennent insupportablement longs.

C'est pourquoi Crystal doit savoir, de manière évidente (aussi évidente que pour un humain),
les types des variables d'instance, de [classe](class_variables.html) et [globales](global_variables.html).

Il y a plusieurs moyens de faire savoir cela à Crystal.

## Utiliser une annotation de type explicite

Le moyen le plus simple, mais sûrement le plus fastidieux, est d'utiliser une annotation de type explicite.

```crystal
class Person
  @name : String
  @age : Int32

  def initialize(@name)
    @age = 0
  end
end
```

## Ne pas utiliser une annotation de type explicite

Si vous ometez l'annotation de type explicite le compilateur va essayer d'inférer le type des variables
d'instance, de classe et globales en utilisant plusieurs règles syntaxiques.

Pour une variable d'instance/classe/globale donnée, quand une règle peut s'appliquer et un type est inféré, le type est ajouté à un ensemble.
Quand plus aucune règle ne peut être appliquée, le type inféré sera l'[union](union_types.html) de ces types.
De plus, si le compilateur déduit qu'une variable d'instance n'est pas toujours initialisée,
il inclura aussi le type [Nil](literals/nil.html).

Les règles sont nombreuses, mais généralement les trois premières sont les plus utilisées.
Il n'y pas besoin de toutes les connaître. Si le compilateur renvoie une erreur signifiant que le type d'une instance ne peut être déduit
vous pouvez toujours ajouter une annotation de type explicite.

Les règles suivantes ne mentionnent que les variables d'instance, mais elles s'appliquent aussi bien aux
variables de classe et globales. Ces règles sont les suivantes.

### 1. Affecter une valeur litérale

Quand un litéral est affecté à une variable d'instance, le type du litéral est ajouté à l'ensemble.
Tous les [litéraux](literals.html) sont associés à un type.

Dans l'exemple qui suit, `@name` est déduit comme une `String` et `@age` comme un `Int32`.

```crystal
class Person
  def initialize
    @name = "John Doe"
    @age = 0
  end
end
```

Cette règle, et toutes les suivantes, seront aussi appliquées dans d'autres méthodes qu'`initialize`. Par exemple:

```crystal
class SomeObject
  def lucky_number
    @lucky_number = 42
  end
end
```

Dans l'exemple suivant, `@lucky_number` sera déduit comme `Int32 | Nil`: `Int32` car 42 lui a été affecté,
et `Nil` car il n'a pas été affecté dans toutes les méthodes initialize de la classe.

### 2. Affecter le résultat de l'invocation de la méthode de classe `new`

Quand une expression comme `Type.new(...)` est affectée à une variable d'instance,
le type `Type` est ajouté à l'ensemble.

Dans l'exemple suivant, `@address` est déduit comme étant `Address`.

```crystal
class Person
  def initialize
    @address = Address.new("somewhere")
  end
end
```

Cela s'applique également aux types génériques.
Ici `@values` est déduit comme étant `Array(Int32)`.

```crystal
class Something
  def initialize
    @values = Array(Int32).new
  end
end
```

**Note**: une méthode `new` peut être redéfinie par un type.
Dans ce cas le type inféré sera celui renvoyé par `new`,
s'il peut être déduit en utilisant les règles suivantes.

### 3. Affecter une variable qui est un argument de méthode avec une restriction de type

Dans l'exemple suivant `@name` est déduit comme étant une `String` car l'argument de méthode `name` a une restriction de type du type `String`,
et cet argument est affecté à `@name`.

```crystal
class Person
  def initialize(name : String)
    @name = name
  end
end
```

Remarquez que le nom de l'argument de la méthode n'est pas important, ce qui suit est tout autant valide:

```crystal
class Person
  def initialize(obj : String)
    @name = obj
  end
end
```

Utiliser la forme courte d'affectation d'une variable d'instance depuis un argument de méthode a le
même effet:

```crystal
class Person
  def initialize(@name : String)
  end
end
```

Remarquez aussi que le compilateur ne vérifie pas si une valeur différente a été ré-affectée à l'argument de méthode:

```crystal
class Person
  def initialize(name : String)
    name = 1
    @name = name
  end
end
```

Dans le cas précédent, le compilateur déduit `@name` comme `String`, et renverra plus tard une erreur à la compilation,
quand il typera complétement la méthode, déclarant que `Int32` ne peut pas être affecté à une variable de type `String`.
Utilisez une annotation de type explicite si `@name` n'est pas supposé être de type `String`.

### 4. Affecter le résultat d'une méthode de classe qui a pour retour une annotation de type

Dans l'exemple suivant, le type de `@address` est inféré comme `Address`, car la méthode de classe `Address.unknown` a pour retour une annotation de type `Address`.

```crystal
class Person
  def initialize
    @address = Address.unknown
  end
end

class Address
  def self.unknown : Address
    new("unknown")
  end

  def initialize(@name : String)
  end
end
```

En fait, le code précédent n'a pas besoin de renvoyer une annotation de type `self.unknown`.
La raison est que le compilateur va aussi regarder le corps d'une méthode de classe et s'il peut appliquer une des règles précédentes
(c'est une méthode `new`, ou c'est un litéral, etc.) il déduira le type de cette expression.
Alors, ce qui précède peut simplement être écrit comme suit:

```crystal
class Person
  def initialize
    @address = Address.unknown
  end
end

class Address
  # No need for a return type annotation here
  def self.unknown
    new("unknown")
  end

  def initialize(@name : String)
  end
end
```

Cette règle supplémentaire est très pratique car il est très commun d'avoir de méthodes de classe "à la constructeur" en plus de `new`.

### 5. Affecter une variable qui est un argument de méthode avec une valeur par déafut

Dans l'exemple qui suit, parce-que la valeur par défaut de `name` est un litéral chaîne de caractères,
et qu'elle est plus tard affectée à `@name`, `String` sera ajouté à l'ensemble des types inférés.

```crystal
class Person
  def initialize(name = "John Doe")
    @name = name
  end
end
```

Cela fonctionne bien sûr aussi avec la syntaxe courte:

```crystal
class Person
  def initialize(@name = "John Doe")
  end
end
```

La valeur par défaut peut aussi être une méthode `Type.new(...)` ou une méthode de classe avec un retour d'annotation de type.

### 6. Affecter le résultat de l'invocation de la fonction `lib`

Parce-qu'une [fonction lib](c_bindings/fun.html) doit avoir des types explicites,
le compilateur peut utiliser le type retour lors de l'affectation à une variable d'instance.

Dans l'exemple suivant `@age` est déduit comme étant `Int32`.

```crystal
class Person
  def initialize
    @age = LibPerson.compute_default_age
  end
end

lib LibPerson
  fun compute_default_age : Int32
end
```

### 7. Utiliser une expression `out` de lib

Parce-qu'une [fonction lib](c_bindings/fun.html) doit avoir des types explicites, le compilateur peut utiliser le type de l'argument `out`,
qui devrait être un type pointeur, et utiliser le type déréférencé deviné.

Dans l'exemple suivant `@age` est déduit comme étant `Int32`.

```crystal
class Person
  def initialize
    LibPerson.compute_default_age(out @age)
  end
end

lib LibPerson
  fun compute_default_age(age_ptr : Int32*)
end
```

### Autres règles

Le compilateur essaiera d'être le plus intelligent possible afin de nécessiter le minimum d'annotations de type.
Par exemple, lors de l'affectation d'une expression `if`, le type sera déduit des branches `then` et `else`:

```crystal
class Person
  def initialize
    @age = some_condition ? 1 : 2
  end
end
```

Parce-que le `if` précédent (en fait, techniquement, un opérateur ternaire, mais c'est similaire à un `if`) a des litéraux d'entier,
`@age` est déduit avec succès comme étant `Int32` sans nécessiter une annotation de type redondante.

Un autre cas est `||` et `||=`:

```crystal
class SomeObject
  def lucky_number
    @lucky_number ||= 42
  end
end
```

Dans l'exemple précédent `@lucky_number` sera déduit comme étant `Int32 | Nil`.
C'est très utile pour des variables sommairement initialisées.

Les constantes seront aussi suivies, étant donné que cela reste assez simple à faire pour le compilateur (et pour un humain).

```crystal
class SomeObject
  DEFAULT_LUCKY_NUMBER = 42

  def initialize(@lucky_number = DEFAULT_LUCKY_NUMBER)
  end
end
```

Ici la règle 5 (valeur par défaut d'argument) est appliquée, et parce-que la constante est résolue en un litéral d'entier,
`@lucky_number` est déduite comme étant `Int32`.
