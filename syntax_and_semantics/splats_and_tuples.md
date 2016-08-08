# Splats et tuples

Une méthode peut recevoir un nombre variable d'arguments en utilisant un *splat* (`*`), qui peut
apparaître une seule fois et à toute position:

```crystal
def sum(*elements)
  total = 0
  elements.each do |value|
    total += value
  end
  total
end

sum 1, 2, 3    #=> 6
sum 1, 2, 3, 4.5 #=> 10.5
```

Les arguments passés deviennent un [Tuple](http://crystal-lang.org/api/Tuple.html) dans le corps de la méthode:

```crystal
# elements devient Tuple(Int32, Int32, Int32)
sum 1, 2, 3

# elements devient Tuple(Int32, Int32, Int32, Float64)
sum 1, 2, 3, 4.5
```

Les arguments après le splat peuvent seulement être placés comme arguments nommés:

```crystal
def sum(*elements, initial = 0)
  total = initial
  elements.each do |value|
    total += value
  end
  total
end

sum 1, 2, 3 # => 6
sum 1, 2, 3, initial: 10 # => 16
```

Les arguments après le splat sans valeur par défaut sont obligatoirement des arguments nommés:

```crystal
def sum(*elements, initial)
  total = initial
  elements.each do |value|
    total += value
  end
  total
end

sum 1, 2, 3 # Error, missing argument: initial
sum 1, 2, 3, initial: 10 # => 16
```

Deux méthodes avec des arguments nommés indispensables différents se surchargent:

```crystal
def foo(*elements, x)
  1
end

def foo(*elements, y)
  2
end

foo x: "something" # => 1
foo y: "something" # => 2
```

L'argument splat peut également ne pas être nommé, avec pour signification "après moi, suivent les arguments nommés":

```crystal
def foo(x, y, *, z)
end

foo 1, 2, 3    # Error, wrong number of arguments (given 3, expected 2)
foo 1, 2       # Error, missing argument: z
foo 1, 2, z: 3 # OK
```

## Splat d'un tuple

Un `Tuple` peut être passé en splat à un appel de méthode en utilisant un `*`:

```crystal
def foo(x, y)
  x + y
end

tuple = {1, 2}
foo *tuple # => 3
```

## Splats double et tuples nommés

Un splat double (`**`) capture les arguments nommés qui n'ont pas été appariés avec d'autres arguments.
Le type de ces arguments est `NamedTuple`:

```crystal
def foo(x, **other)
  # Renvoie les arguments nommés capturés en tant que NamedTuple
  other
end

foo 1, y: 2, z: 3    # => {y: 2, z: 3}
foo y: 2, x: 1, z: 3 # => {y: 2, z: 3}
```

## Double splat d'un tuple nommé

Un `NamedTuple` peut être passé en splat dans un appel de méthode en utilisant `**`:

```crystal
def foo(x, y)
  x - y
end

tuple = {y: 3, x: 10}
foo **tuple # => 7
```
