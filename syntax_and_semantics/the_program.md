# Le Programme

Le programme est un objet global dans lequel vous pouvez définir des types, méthodes et des variables locales au fichier.

```crystal
# Définit une méthode dans le programme
def add(x, y)
  x + y
end

# Invoque la méthode add dans le programme
add(1, 2) #=> 3
```

La valeur d'une méthode est la valeur de sa dernière expression, il n'y a pas besoin
d'utiliser explicitement de `return`. Néanmoins, un `return` explicite est possible:

```crystal
def even?(num)
  if num % 2 == 0
    return true
  end

  return false
end
```

A l'invocation d'une méthode sans objet destinataire, comme `add(1, 2)`,
elle sera recherchée dans le programme si elle ne peut être trouvée dans le type courant
ni aucun de ses parents.

```crystal
def add(x, y)
  x + y
end

class Foo
  def bar
    # invoque la méthode add du programme
    add(1, 2)

    # invoque la méthode baz de Foo
    baz(1, 2)
  end

  def baz(x, y)
    x * y
  end
end
```

Si vous voulez invoquer la méthode du programme, même si le type courant définit une méthode avec le même nom,
préfixez l'appel avec un `::`:

```crystal
def baz(x, y)
  x + y
end

class Foo
  def bar
    baz(4, 2) #=> 2
    ::baz(4, 2) #=> 6
  end

  def baz(x, y)
    x - y
  end
end
```

Les variables déclarées dans un programme ne sont pas accessibles depuis les méthodes:

```crystal
x = 1

def add(y)
  x + y # error: undefined local variable or method 'x'
end

add(2)
```

Les parenthèses sont optionnelles dans les appels de méthode:

```crystal
add 1, 2 # équivalent à add(1, 2)
```

## Code principal

Le code principal, le code exécuté quand vous compilez et exécutez le programme,
peut être écrit directement dans le fichier source sans avoir à l'inclure dans une méthode "main" spéciale:

```crystal
# Un programme qui affiche "Hello Crystal!"
puts "Hello Crystal!"
```

Le code principal peut aussi être placé entre des déclarations:

```crystal
# Un programme qui affiche "Hello Crystal!"
class Hello
  # 'self' ici est la classe Hello class
  puts self
end
```
