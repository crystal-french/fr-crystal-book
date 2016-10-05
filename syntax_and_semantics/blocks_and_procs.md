# Blocs et Procs

Les méthodes acceptent un bloc de code qui sera exécuté
avec le mot clé `yield`. Par exemple:

```crystal
def twice
  yield
  yield
end

twice do
  puts "Hello!"
end
```

Le programme précédent affiche "Hello!" deux, une fois pour chaque `yield`.

Pour définir une méthode qui reçoit un bloc, utilisez simplement `yield` depuis celle-ci pour le faire savoir au compilateur.
Vous pouvez le faire de manière encore plus explicite en déclarant un argument de bloc factice, donné en tant que dernier argument préfixé avec un esperluette (`&`):

```crystal
def twice(&block)
  yield
  yield
end
```

Pour invoquer une méthode et passer un bloc, utilisez `do ... end` ou `{ ... }`. Ce qui suit a un effet équivalent:

```crystal
twice() do
  puts "Hello!"
end

twice do
  puts "Hello!"
end

twice { puts "Hello!" }
```

La différence entre `do ... end` et `{ ... }` est que `do ... end` s'applique à l'appel le plus à gauche, alors que `{ ... }` s'applique à l'appel le plus à droite:

```crystal
foo bar do
  something
end

# Ce qui précéde est équivalent à
foo(bar) do
  something
end

foo bar { something }

# Ce qui précéde est équivalent à

foo(bar { something })
```

Cela a pour raison de permettre de créer des DSLs (Domain Specific Languages) en utilisant `do ... end` afin de les rendre lisibles en anglais:

```crystal
open file "foo.cr" do
  something
end

# Equivalent à:
open(file("foo.cr")) do
end
```

Vous ne voudriez pas que ce qui précéde soit équivalent à:

```crystal
open(file("foo.cr") do
end)
```

## Surcharge

Deux méthodes, une qui yielde et l'autre non, sont considérées comme différentes surcharge, comme expliqué dans la section [surcharge](overloading.html).

## Argumes de yielde

L'expression `yield` est l'équivalent d'un appel et peut prendre des arguments. Par exemple:

```crystal
def twice
  yield 1
  yield 2
end

twice do |i|
  puts "Got #{i}"
end
```

Ce qui précéde affiche "Got 1" et "Got 2".

Une notation avec des accolades est aussi possible:

```crystal
twice { |i| puts "Got #{i}" }
```

Un `yield` peut prendre plusieurs valeurs:

```crystal
def many
  yield 1, 2, 3
end

many do |x, y, z|
  puts x + y + z
end

# Output: 6
```

Un bloc peut spécifier moins que les arguments à utiliser par le yield:

```crystal
def many
  yield 1, 2, 3
end

many do |x, y|
  puts x + y
end

# Output: 3
```

C'est une erreur que de spécifier plus d'arguments au bloc que ceux à utiliser par le yield:

```crystal
def twice
  yield
  yield
end

twice do |i| # Error: too many block arguments
end
```

Chaque variable de bloc a le type de chaque expression du yield à cette même position. Exemple:

```crystal
def some
  yield 1, 'a'
  yield true, "hello"
  yield 2, nil
end

some do |first, second|
  # first is Int32 | Bool
  # second is Char | String | Nil
end
```

La variable de block `second` inclut également le type `Nil` car la dernière expression `yield` n'inclut pas de second argument.

## Syntaxe argument unique courte

Une syntaxe courte existe pour spécifier un bloc qui reçoit un unique argument et invoque une méthode avec. Ceci:

```crystal
method do |argument|
  argument.some_method
end
```

Peut être écrit ainsi:

```crystal
method &.some_method
```

Ou comme ça:

```crystal
method(&.some_method)
```

Ce qui précéde est simplement du sucre syntaxique et n'impacte aucunement les performances.

Les arguments peuvent aussi bien être passés à `some_method`:

```crystal
method &.some_method(arg1, arg2)
```

Et les opérateurs peuvent tout aussi bien être invoqués également:

```crystal
method &.+(2)
method &.[index]
```

## Valeur d'un yield

L'expression `yield` elle-même a une valeur: la dernière expression du bloc. Par exemple:

```crystal
def twice
  v1 = yield 1
  puts v1

  v2 = yield 2
  puts v2
end

twice do |i|
  i + 1
end
```

Ce qui précéde affiche "2" et "3".

La valeur d'une expression `yield` est surtout utile pour transformer et filtrer les valeurs.
Les meilleurs exemples de ça sont les [Enumerable#map](http://crystal-lang.org/api/Enumerable.html#map%28%26block%20%3A%20T%20-%3E%20U%29-instance-method)
et les [Enumerable#select](http://crystal-lang.org/api/Enumerable.html#select%28%26block%20%3A%20T%20-%3E%20%29-instance-method):

```crystal
ary = [1, 2, 3]
ary.map { |x| x + 1 }         #=> [2, 3, 4]
ary.select { |x| x % 2 == 1 } #=> [1, 3]
```

Une méthode de tranformation factice:

```crystal
def transform(value)
  yield value
end

transform(1) { |x| x + 1 } #=> 2
```

Le résultat de la dernière expression est `2` car la dernière expression de la méthode `transform` est `yield`, dont la valeur est la dernière expression du bloc.

## break

Une expression `break` dans un bloc fait retourner plus tôt la méthode:

```crystal
def thrice
  puts "Before 1"
  yield 1
  puts "Before 2"
  yield 2
  puts "Before 3"
  yield 3
  puts "After 3"
end

thrice do |i|
  if i == 2
    break
  end
end
```

Ce qui précéde affiche "Before 1" et "Before 2". La méthode `thrice` n'a pas exécuté l'expression `puts "Before 3"` à cause du `break`.

`break` peut aussi accepter des arguments: ils deviennent les valeurs de retour de la méthode. Par exemple:

```crystal
def twice
  yield 1
  yield 2
end

twice { |i| i + 1 } #=> 3
twice { |i| break "hello" } #=> "hello"
```

La valeur du premier appel est 3 car la dernière expression de la méthode `twice` est `yield`,
qui prend la valeur du bloc. La valeur du second appel est "hello" car un `break` a eu lieu.

Si il y a des breaks conditionnels, la valeur de retour de l'appel sera l'union du type de la valeur du bloc et du type des différents `break`:

```crystal
value = twice do |i|
  if i == 1
    break "hello"
  end
  i + 1
end
value #:: Int32 | String
```

Si un `break` reçoit plusieurs arguments, ils sont automatiquement transformés en [Tuple](http://crystal-lang.org/api/Tuple.html):

```crystal
values = twice { break 1, 2 }
values #=> {1, 2}
```

Si un `break` ne reçoit aucun argument, c'est équivalent à recevoir un seul argument `nil`:

```crystal
value = twice { break }
value #=> nil
```

## next

L'expression `next` dans un bloc provoque une sortie prématurée du bloc (pas de la méthode). Par exemple:

```crystal
def twice
  yield 1
  yield 2
end

twice do |i|
  if i == 1
    puts "Skipping 1"
    next
  end

  puts "Got #{i}"
end

# Ouptut:
# Skipping 1
# Got 2
```

L'expression `next` accepte des arguments, et ceux-ci sont pris comme valeur du `yield` invoqué par le bloc:

```crystal
def twice
  v1 = yield 1
  puts v1

  v2 = yield 2
  puts v2
end

twice do |i|
  if i == 1
    next 10
  end

  i + 1
end

# Output
# 10
# 3
```

Si un `next` reçoit plusieurs arguments, ils sont automatiquement transformés en [Tuple](http://crystal-lang.org/api/Tuple.html).
Si il ne reçoit aucun argument c'est équivalent à recevoir un seul argument `nil`.

## with ... yield

Une expression `yield` peut être modifiée, en utilisant le mot clé `with`,
pour spécifier l'objet à utiliser comme récepteur par défaut des appels de méthode du bloc:

```crystal
class Foo
  def one
    1
  end

  def yield_with_self
    with self yield
  end

  def yield_normally
    yield
  end
end

def one
  "one"
end

Foo.new.yield_with_self { one } # => 1
Foo.new.yield_normally { one }  # => "one"
```

## Dépaquetez des arguments bloc

Un argument bloc peut spécifier des sous-arguments inclus entre parenthèses:

```crystal
array = [{1, "one"}, {2, "two"}]
array.each do |(number, word)|
  puts "#{number}: #{word}"
end
```

Ce qui précéde est du sucre syntaxique équivalent à:

```crystal
array = [{1, "one"}, {2, "two"}]
array.each do |arg|
  number = arg[0]
  word = arg[1]
  puts "#{number}: #{word}"
end
```

Cela signifie que tout type qui répond à `[]` avec des entiers peut être dépaqueté en argument de bloc.

## Performance

Quand vous utilisez des blocs avec `yield`, les blocs sont **toujours** inline:
closures, appels ou pointeurs de fonction ne sont associés. Cela signifie que ceci:

```crystal
def twice
  yield 1
  yield 2
end

twice do |i|
  puts "Got: #{i}"
end
```

est exactement égal à cela:

```crystal
i = 1
puts "Got: #{i}"
i = 2
puts "Got: #{i}"
```

Par exemple, la librairie standard inclut une méthode `times` sur des entiers, vous permettant d'écrire:

```crystal
3.times do |i|
  puts i
end
```

Ça a l'air fort sympathique, mais est-ce réellement aussi rapide qu'une boucle C?
La réponse est: oui!

Voilà la définition de `Int#times`:

```crystal
struct Int
  def times
    i = 0
    while i < self
      yield i
      i += 1
    end
  end
end
```

Parce-qu'un bloc non capturé est toujours inline, l'invocation de la méthode précédente est **exactement identique** à:

```crystal
i = 0
while i < 3
  puts i
  i += 1
end
```

N'ayez pas d'utiliser les blocs pour la lisibilité ou la ré-utilisation de code, cela n'affectera pas les performances de l'exécutable généré.
