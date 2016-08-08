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

Tout ça parce-que lrosque vous appelez `add`, elle est instanciée avec les types des arguments:
chaque appel de méthode avec une combinaison différente de type résulte en un instantiation différente de méthode.

La seule différence est que le premier message est légérement plus clair, mais chaque définition est valide dans le sens où vous aurez une erreur à la compilation
quand même. Donc, en général, il est préférable de ne pas spécifier les restrictions de type et de les utiliser principalement pour définir les surcharges de méthodes.
Par exemple, si on définit une classe qui a une méthode `+` mais qui n'est pas un `Number`, on peut utiliser méthode `add` qui n'a pas de restriction de type,
mais on ne peut utiliser la méthode `add` qui a des restrictions.

```crystal
# Une classe qui a une méthode + mais qui n'est pas un Number
class Six
  def +(other)
    6 + other
  end
end

# méthode add sans restrictions de type
def add(x, y)
  x + y
end

# OK
add Six.new, 10

# méthode add avec restrictions de type
def restricted_add(x : Number, y : Number)
  x + y
end

# Error: no overload matches 'restricted_add' with types Six, Int32
restricted_add Six.new, 10
```

Refer to the [type grammar](type_grammar.html) for the notation used in type restrictions.

## self restriction

A special type restriction is `self`:

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

In the previous example `self` is the same as writing `Person`. But, in general, `self` is the same as writing the type that will finally own that method, which, when modules are involved, becomes more useful.

As a side note, since `Person` inherits `Reference` the second definition of `==` is not needed, since it's already defined in `Reference`.

Note that `self` always represents a match against an instance type, even in class methods:

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

You can use `self.class` to restrict to the Person type. The next section talks about the `.class` suffix in type restrictions.

## Classes as restrictions

Using, for example, `Int32` as a type restriction makes the method only accept instances of `Int32`:

```crystal
def foo(x : Int32)
end

foo 1       # OK
foo "hello" # Error
```

If you want a method to only accept the type Int32 (not instances of it), you use `.class`:

```crystal
def foo(x : Int32.class)
end

foo Int32  # OK
foo String # Error
```

The above is useful for providing overloads based on types, not instances:

```crystal
def foo(x : Int32.class)
  puts "Got Int32"
end

def foo(x : String.class)
  puts "Got String"
end

foo Int32  # prints "Got Int32"
foo String # prints "Got String"
```

## Type restrictions in splats

You can specify type restrictions in splats:

```crystal
def foo(*args : Int32)
end

def foo(*args : String)
end

foo 1, 2, 3       # OK, invokes first overload
foo "a", "b", "c" # OK, invokes second overload
foo 1, 2, "hello" # Error
foo()             # Error
```

When specifying a type, all elements in a tuple must match that type. Additionally, the empty-tuple doesn't match any of the above cases. If you want to support the empty-tuple case, add another overload:

```crystal
def foo
  # This is the empty-tuple case
end
```

A simple way to match against one or more elements of any type is to use `Object` as a restriction:

```crystal
def foo(*args : Object)
end

foo() # Error
foo(1) # OK
foo(1, "x") # OK
```

## Free variables

If you use a single uppercase letter as a type restriction, the identifier becomes a free variable:

```crystal
def foo(x : T)
  T
end

foo(1)       #=> Int32
foo("hello") #=> String
```

That is, `T` becomes the type that was effectively used to instantiate the method.

A free variable can be used to extract the type parameter of a generic type within a type restriction:

```crystal
def foo(x : Array(T))
  T
end

foo([1, 2])   #=> Int32
foo([1, "a"]) #=> (Int32 | String)
```

To create a method that accepts a type name, rather than an instance of a type, append `.class` to a free variable in the type restriction:

```crystal
def foo(x : T.class)
  Array(T)
end

foo(Int32)  #=> Array(Int32)
foo(String) #=> Array(String)
```

## Free variables in constructors

Free variables allow type inference to be used when creating generic types. Refer to the [Generics](generics.html) section.

