# Restrictions de type

Les restrictions de type sont des annotations de type sur des arguments de méthode pour limiter les
types acceptés par cette méthode.

```crystal
def add(x : Number, y : Number)
  x + y
end

# Ok
add 1, 2 # Ok

# Error: no overload matches 'add' with types Bool, Bool
add true, false
```

Remarquez que si nous avions défini `add` sans restriction de type,
nous aurions également eu une erreur de compilation:

```crystal
def add(x, y)
  x + y
end

add true, false
```

Le code précédent renvoie une erreur à la compilation:

```
Error in foo.cr:6: instantiating 'add(Bool, Bool)'

add true, false
^~~

in foo.cr:2: undefined method '+' for Bool

  x + y
    ^
```

Tout ça parce-que lorsque vous appelez `add`, elle est instanciée avec les types des arguments:
chaque appel de méthode avec une combinaison différente de type résulte en une instantiation différente de méthode.

La seule différence est que le premier message est légérement plus clair, mais chaque définition est valide dans le sens où vous aurez une erreur à la compilation
quand même. Donc, en général, il est préférable de ne pas spécifier les restrictions de type et de les utiliser principalement pour définir les surcharges de méthodes.
Par exemple, si on définit une classe qui a une méthode `+` mais qui n'est pas un `Number`, on peut utiliser la méthode `add` qui n'a pas de restriction de type,
mais on ne peut utiliser la méthode `add` qui a des restrictions.

```crystal
# Une classe qui a une méthode + mais qui n'est pas un Number
class Six
  def +(other)
    6 + other
  end
end

# méthode add sans restriction de type
def add(x, y)
  x + y
end

# OK
add Six.new, 10

# méthode add avec restriction de type
def restricted_add(x : Number, y : Number)
  x + y
end

# Error: no overload matches 'restricted_add' with types Six, Int32
restricted_add Six.new, 10
```

Reportez-vous à la [grammaire de type](type_grammar.html) pour la notation utilisée dans les restrictions de type.

## Restriction self

`self` est une restriction de type spéciale:

```crystal
class Person
  def ==(other : self)
    other.name == name
  end

  def ==(other)
    false
  end
end

john = Person.new "John"
another_john = Person.new "John"
peter = Person.new "Peter"

john == another_john #=> true
john == peter #=> false (names differ)
john == 1 #=> false (because 1 is not a Person)
```

Dans l'exemple précédent `self` revient à écrire `Person`. Mais, en general, `self` est équivalent à écrire le type qui possédera au final la méthode,
ce qui, lorsque des modules sont impliqués, devient plus utile.

Notez au passage que étant donné que `Person` hérite de `Reference` la seconde définition de `==` est inutile, car déjà définie dans `Reference`.

Notez que `self` représente toujours une correspondance avec un type d'instance, même dans les méthodes de classe:

```crystal
class Person
  def self.compare(p1 : self, p2 : self)
    p1.name == p2.name
  end
end

john = Person.new "John"
peter = Person.new "Peter"

Person.compare(john, peter) # OK
```

Vous pouvez utiliser `self.class` pour vous restreindre au type Person.
La section suivante présente le suffixe `.class` dans les restrictions de type.

## Classes comme restrictions

En utilisant, par exemple, `Int32` comme restriction de type fait que la méthode n'accepte que des instances de `Int32`:

```crystal
def foo(x : Int32)
end

foo 1       # OK
foo "hello" # Error
```

Si vous voulez qu'une méthode n'accepte que le type Int32 (et pas des instances), utilisez `.class`:

```crystal
def foo(x : Int32.class)
end

foo Int32  # OK
foo String # Error
```

Ce qui précéde est utile pour fournir des surcharges basées sur les types, et non sur les instances:

```crystal
def foo(x : Int32.class)
  puts "Got Int32"
end

def foo(x : String.class)
  puts "Got String"
end

foo Int32  # affiche "Got Int32"
foo String # affiche "Got String"
```

## Restrictions de type dans des splats

Vous pouvez spécifier des restrictions de type dans des splats:

```crystal
def foo(*args : Int32)
end

def foo(*args : String)
end

foo 1, 2, 3       # OK, invoque la première surcharge
foo "a", "b", "c" # OK, invoque la seconde surcharge
foo 1, 2, "hello" # Erreur
foo()             # Erreur
```

Quand vous spécifiez un type, tous les éléments d'un tuple doivent correspondre à ce type. De plus, le tuple vide ne correspond à aucun des cas précédents.
Si vous voulez supporter le cas du tuple vide, ajoutez une surcharge supplémentaire:

```crystal
def foo
  # Voici le cas du tuple vide
end
```

Un moyen simple de faire la correspondance avec un ou plusieurs éléments de tout type est d'utiliser `Object` comme restriction:

```crystal
def foo(*args : Object)
end

foo() # Error
foo(1) # OK
foo(1, "x") # OK
```

## Variables libres

Si vous utilisez une seule lettre majuscule comme restriction de type, l'identifiant devient une variable libre:

```crystal
def foo(x : T)
  T
end

foo(1)       #=> Int32
foo("hello") #=> String
```

Ainsi, `T` devient le type qui a été utilisé concrétement pour instancier la méthode.

Une variable libre peut être utilisée pour extraire le paramètre du type d'un type générique dans une restriction de type:

```crystal
def foo(x : Array(T))
  T
end

foo([1, 2])   #=> Int32
foo([1, "a"]) #=> (Int32 | String)
```

Pour créer une méthode qui accepte un nom de type, plutôt qu'une instance de type, ajoutez `.class` à une variable libre dans la restriction de type:

```crystal
def foo(x : T.class)
  Array(T)
end

foo(Int32)  #=> Array(Int32)
foo(String) #=> Array(String)
```

## Variables dans les constructeurs

Les variables libres permettent d'utiliser l'inférence(voir définition en fin de page), de type lors de la création des types génériques. Reportez-vous à la section sur les [Génériques](generics.html).

inférence: Opération par laquelle on passe d'une assertion considérée comme vraie a une autre assertion au moyen d'un système de règles qui rend cette deuxième assertion également vraie.
