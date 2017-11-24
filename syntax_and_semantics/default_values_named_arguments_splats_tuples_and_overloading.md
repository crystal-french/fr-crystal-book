# Arguments de méthode

Voici la spécification formelle des arguments d'appel et méthode.

## Contenu d'une définition de méthode

Une défintion de méthode se compose de:

* Arguments positionnels facultatifs et non-facultatifs,
* Un argument splat optionnel, dont le nom peut être nul,
* Arguments nommés requis et facultatifs,
* Un argument splat double optionnel.

Par exemple:

```crystal
def foo(
  # Des arguments positionnels:
  x, y, z = 1,
  # Un argument splat:
  *args,
  # Des arguments nommés:
  a, b, c = 2,
  # Un argument splat double:
  **options
  )
end
```

Chacun d'entre eux est optionnel, une méthode peut donc se passer du splat double, du splat, des mot-clés d'arguments et des arguments positionnels.

## Composition d'un appel de méthode

Un appel de méthode possède aussi certains composants:

```crystal
foo(
  # Des arguments positionnels
  1, 2,
  # Des arguments nommés
  a: 1, b: 2
)
```

De plus, un argument d'appel peut avoir un splat (`*`) ou un splat double (`**`). Un splat convertit un [Tuple](literals/tuple.html)
en arguments positionnels, alors qu'un splat double convertit un [NamedTuple](literals/named_tuple.html) en arguments nommés.
Plusieurs arguments splats et splats doubles sont autorisés.

## Comment les arguments d'appel sont appariés avec les arguments de méthode

Lors de l'invocation d'une méthode, l'algorithme pour apparier les arguments de l'appel
avec les arguments de méthodes est:

* D'abord les arguments postionnels sont appariés avec les arguments postionnels de la méthode.
  Le nombre d'entre eux doit être au moins égal au nombre d'arguments positionnels sans valeur par défaut.
  S'il y a un argument de méthode splat avec un nom (le cas où il n'y a pas de nom est vu plus bas), plus
  d'arguments positionnels sont autorisés et sont capturés en tant que tuple.
  Les arguments positionnels ne sont jamais appariés après l'argument de méthode splat.
* Ensuite les arguments nommés sont appariés, par nom, avec les arguments de la méthode (après comme avant l'argument splat de méthode).
  Si un argument a déjà été rempli par un argument positionnel, alors il y a erreur.
* Les arguments nommés supplémentaires sont placés dans l'argument de méthode splat double, en tant que [NamedTuple](literals/named_tuple.html),
  s'il existe, sinon c'est une erreur.

Quand un argument de méthode splat n'a pas de nom, cela signifie que plus aucun arguments positionnels ne peuvent être passés,
et les prochains arguments doivent être passés en tant qu'arguments nommés. Par exemple:

```crystal
# Seulement un argument positionnel autorisé, y doit être passé en tant qu'argument nommé
def foo(x, *, y)
end

foo 1 # Error, missing argument: y
foo 1, 2 # Error: wrong number of arguments (given 2, expected 1)
foo 1, y: 10 # OK
```

Mais même si un argument de méthode splat a un nom, les arguments qui suivent doivent être passés en tant qu'arguments nommés:

```crystal
# Un ou plus d'arguments positionnesl autorisés, y doit être passé en tant qu'argument nommé
def foo(x, *args, y)
end

foo 1 # Error, missing argument: y
foo 1, 2 # Error: missing argument; y
foo 1, 2, 3 # Error: missing argument: y
foo 1, y: 10 # OK
foo 1, 2, 3, y: 4 # OK
```

Il y a aussi la possibilité de faire qu'une méthode reçoive uniquement des arguments nommés (et les liste),
en plaçant une étoile au début:

```crystal
# Une méthode avec deux arguments nommés requis: x et y
def foo(*, x, y)
end

foo # Error: missing arguments: x, y
foo x: 1 # Error: missing argument: y
foo x: 1, y: 2 # OK
```

Les arguments après l'étoile peuvent également avoir des valeurs par défaut.
Cela signifie: ils doivent être passés en tant qu'arguments nommés,
mais ils ne sont pas requis (voir: arguments nommés optionnels):

```crystal
# Une méthode avec deux arguments nommés requis: x et y
def foo(*, x, y = 2)
end

foo # Error: missing argument: x
foo x: 1 # OK, y is 2
foo x: 1, y: 3 # OK, y is 3
```

Parce-que les arguments (sans valeur par défaut) après l'argument de méthode splat doivent être passés par nom,
deux méthodes avec des arguments nommés requis se surchargent:

```crystal
def foo(*, x)
  puts "Passed with x: #{x}"
end

def foo(*, y)
  puts "Passed with y: #{y}"
end

foo x: 1 # => Passed with x: 1
foo y: 2 # => Passed with y: 2
```

Les arguments positionnels peuvent toujours être appariés par nom:

```crystal
def foo(x, *, y)
end

foo 1, y: 2 # OK
foo y: 2, x: 3 # OK
```

## Noms externes

Un nom externe peut être spécifié pour un argument de méthode.
Le nom externe est celui utilisé quand un argument est passé comme argument nommé,
et le nom interne est celui utilisé dans la définition de la méthode:

```crystal
def foo(external_name internal_name)
  # ici nous utilisons internal_name
end

foo external_name: 1
```

Cela couvre deux cas d'utilisation.

Le premier cas est l'utilisation de mots-clefs comme arguments nommés:

```crystal
def plan(begin begin_time, end end_time)
  puts "Planning between #{begin_time} and #{end_time}"
end

plan begin: Time.now, end: 2.days.from_now
```

Le second cas est de rendre plus lisible un argument de méthode dans le corps d'une méthode:

```crystal
def increment(value, by)
  # Bien, mais bizarre à lire
  value + by
end

def increment(value, by amount)
  # Mieux
  value + amount
end
```
