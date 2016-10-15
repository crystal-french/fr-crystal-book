# Types Union

Le type d'une variable ou expression peut être multiple.
On l'appelle un type union. Par exemple,
lors d'une affectation d'une même variable dans différentes branches d'un [if](if.html):

```crystal
if 1 + 2 == 3
  a = 1
else
  a = "hello"
end

a # : Int32 | String
```

A la fin d'un if, `a` aura le type `Int32 | String`,
comprenez "l'union d'un Int32 et une String". Ce type union est créé automatiquement par le compilateur.
A l'exécution, `a` sera bien sûr d'un seul type.
On peut le voir en invoquant la méthode `class`:

```crystal
# Type à l'exécution
a.class # => Int32
```

Le type à la compilation peut être connu en utilisant [typeof](typeof.html):

```crystal
# Le type à la compilation
typeof(a) # => Int32 | String
```

Une union consiste en un nombre arbitrairement large de types.
A l'invocation d'une méthode sur une expression d'un type union,
tous les types de l'union doivent répondre à la méthode,
sinon une erreur à la compilation sera renvoyée.
Le type à l'appel de la méthode est le type union des types de retour de ces méthodes.

```crystal
# to_s est défini pour Int32 et String, il renvoie une String
a.to_s # => String

a + 1 # Erreur, car String#+(Int32) n'est pas défini
```

Si nécessaire, une variable eput être définie comme type union au moment de la compilation:

``
# définit le type à la compilation
a = 0.as(Int32|Nil|String)
typeof(a) # => Int32 | Nil | String
```

## Règles des types union

En règle générale, quand deux types `T1` et `T2` sont combinés, le résultat est l'union `T1 | T2`.
Cependant, il y a quelques cas où le type renvoyé est un type différent.

### Union de classes et structs dans une même hiérarchie

Si `T1` et `T2` sont sous la même hiérarchie, et leur ancêtre commun le plus proche `Parent` n'est pas `Reference`,
`Struct`, `Int`, `Float` ou `Value`, le type résultat est `Parent+`.
Ceci est appelé un type virtuel, ce qui signifie simplement que le compilateur
verra le type comme étant `Parent` ou n'importe lequel de ses sous-types.

Par exemple:

```crystal
class Foo
end

class Bar < Foo
end

class Baz < Foo
end

bar = Bar.new
baz = Baz.new

# Ici le type de foo sera Bar | Baz,
# mais parce-que Bar comme Baz hérite de Foo,
# le type renvoyé est Foo+
foo = rand < 0.5 ? bar : baz
typeof(foo) # => Foo+
```

### Union de tuples de même taille

L'union de deux tuples de même taille résulte en un type de tuple
qui a l'union des types de chaque position.

Par exemple:

```crystal
t1 = {1, "hi"}   # Tuple(Int32, String)
t2 = {true, nil} # Tuple(Bool, Nil)

t3 = rand < 0.5 ? t1 : t2
typeof(t3) # Tuple(Int32 | Bool, String | Nil)
```

### Union de tuples nommés avec les mêmes clefs

L'union de deux tuples nommés avec les même clefs (indifféremment de leur ordre) résulte en un type de tuple nommé qui est l'union
des types dans chaque clef. L'ordre des clés sera celui du tuple de gauche.

Par exemple:

```crystal
t1 = {x: 1, y: "hi"}   # Tuple(x: Int32, y: String)
t2 = {y: true, x: nil} # Tuple(y: Bool, x: Nil)

t3 = rand < 0.5 ? t1 : t2
typeof(t3) # NamedTuple(x: Int32 | Nil, y: String | Bool)
```
