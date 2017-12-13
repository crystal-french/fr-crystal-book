# Gestion des exceptions

Crystal gère les exceptions en levant et récupérant les exceptions.

## Lever une exception

Vous levez des exceptions en invoquant la méthode de haut niveau `raise`.
Contrairement à d'autres mots clés, `raise` est une méthode normale avec deux surcharges:
[une acceptant une String](http://crystal-lang.org/api/toplevel.html#raise%28message%20%3A%20String%29-class-method)
et un autre [acceptant une instance exception](http://crystal-lang.org/api/toplevel.html#raise%28ex%20%3A%20Exception%29-class-method):

```crystal
raise "OH NO!"
raise Exception.new("Some error")
```

La version String crée simplement une nouvelle instance d'[Exception](http://crystal-lang.org/api/Exception.html) avec ce message.

Seules les instances ou sous-classes d'`Exception` peuvent être levées.

## Définir des exceptions personnalisées

Pour définir un type personnalisé d'exception, créez simplement une sous-classe à partir d'[Exception](http://crystal-lang.org/api/Exception.html):

```crystal
class MyException < Exception
end

class MyOtherException < Exception
end
```

Vous pouvez, comme d'habitude, définir un constructeur pour votre exception ou simplement utiliser celui par défaut.

## Récupérer des exceptions

Pour récupérer toute exception utilisez une expression `begin ... rescue ... end`:

```crystal
begin
  raise "OH NO!"
rescue
  puts "Rescued!"
end

# Output: Rescued!
```

Pour accéder à une exception récupérée vous pouvez spécifier une variable dans la clause `rescue`:

```crystal
begin
  raise "OH NO!"
rescue ex
  puts ex.message
end

# Output: OH NO!
```

Pour récupérer seulement un type d'exception (ou n'importe laquelle de ses sous-classes):

```crystal
begin
  raise MyException.new("OH NO!")
rescue MyException
  puts "Rescued MyException"
end

# Output: Rescued MyException
```

Et pour y accéder, utilisez une syntaxe similaire aux restrictions de type:

```crystal
begin
  raise MyException.new("OH NO!")
rescue ex : MyException
  puts "Rescued MyException: #{ex.message}"
end

# Output: Rescued MyException: OH NO!
```

Des clauses multiples `rescue` peuvent être spécifiées:

```crystal
begin
  # ...
rescue ex1 : MyException
  # only MyException...
rescue ex2 : MyOtherException
  # only MyOtherException...
rescue
  # any other kind of exception
end
```

Vous pouvez aussi récupérer des types multiples d'exception d'un coup en spécifiant un type union:

```crystal
begin
  # ...
rescue ex : MyException | MyOtherException
  # only MyException or MyOtherException
rescue
  # any other kind of exception
end
```

## ensure

Une clause `ensure` est exécutée à la fin d'une expression `begin ... end` ou `begin ... rescue ... end`
qu'une exception ait été levée ou non:

```crystal
begin
  chose_dangereuse
ensure
  puts "Cleanup..."
end

# Va écrire "Cleanup..." après avoir invoqué chose_dangereuse,
# que ce soit levé ou non
```

Ou:

```crystal
begin
  chose_dangereuse
rescue
  # ...
ensure
  # cela va être toujours exécuter
end
```

Les clauses `ensure` sont généralement utilisées pour faire du ménage, libérer des ressources, etc.

## else

Une clause `else` est exécutée seulement si aucune exception n'a été levée:

```crystal
begin
  chose_dangereuse
rescue
  # execute si une exception est levée
else
  # execute si une exception n'est pas levée
end
```

Une clause `else` peut seulement être spécifiée si au moins une clause `rescue` est spécifiée.

## Forme de syntaxe courte

La gestion d'exception possède une forme de syntaxe courte:
elle assume qu'une définition de méthode est une expression implicite `begin ... end`,
puis spécifie les clauses `rescue`, `ensure` et `else`:

```crystal
def some_method
  chose_dangereuse
rescue
  # execute si une exception est levée
end

# L'exemple précédent est équivalent à:
def some_method
  begin
    chose_dangereuse
  rescue
    # execute si une exception est levée
  end
end
```

Un exemple avec `ensure`:

```crystal
def some_method
  chose_dangereuse
ensure
  # s'exécute toujours
end

# L'exemple précédent est équivalent à:
def some_method
  begin
    chose_dangereuse
  ensure
    # s'exécute toujours
  end
end
```

## Inférence de type

Les variables déclarées dans une partie `begin` d'une gestion d'exception ont aussi le type `Nil` quand elles sont considérées dans un bloc `rescue` ou `ensure`.
Par exemple:

```crystal
begin
  a = chose_dangereuse_qui_retourne_Int32
ensure
  puts a + 1 # error, undefined method '+' for Nil
end
```

Ce qui précéde arrive si `chose_dangereuse_qui_retourne_Int32` ne léve jamais d'exception,
ou si une valeur a été assignée à `a` puis une méthode qui peut lever une exception est levée:

```crystal
begin
  a = 1
  chose_dangereuse
ensure
  puts a + 1 # error, undefined method '+' for Nil
end
```

Bien qu'il est évident que `a` se verra toujours affecter une valeur,
le compilateur pensera toujours que `a` n'aura jamais une chance d'être initialisée.
Bien que cette logique puisse évoluer à l'avenir, pour l'instant cela vous force à laisser vos gestions d'exception à leur strict minimum,
rendant le but de votre code plus clair:

```crystal
# Plus clair que ce qui précède: `a` n'a pas besoin
# d'être dans le code de gestion des exceptions.
a = 1
begin
  chose_dangereuse
ensure
  puts a + 1 # ça marche
end
```

## Alternatives à la gestion d'erreur

Bien que les exceptions soient disponibles comme l'un des mécanismes de gestion d'erreur, ce n'est pas votre unique choix.
Lever une exception implique l'allocation de mémoire, et exécuter une gestion d'exception est généralement lent.

La librairie standard fournit généralement plusieurs méthodes pour accomplir quelque chose: une lève, l'autre retourne `nil`. Par exemple:

```crystal
array = [1, 2, 3]
array[4]  # lève à cause d'IndexError
array[4]? # retourne nil à cause de l'index hors limites
```

La convention habituelle est de fournir une méthode alternative "question" pour indiquer que
cette variante de la méthode retourne `nil` au lieu de lever une exception.
Cela permet à l'utilisatrice de choisir entre travailler avec des exceptions ou avec `nil`.
Remarquez, cependant, que ce n'est pas disponible pour toutes les méthodes existantes, étant donné que les exceptions
sont à privilégier car elles ne polluent pas votre code avec la logique de gestion d'erreurs.
