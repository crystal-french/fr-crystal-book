# Macros

Les Macros sont des méthodes qui reçoivent des noeuds d'arbre syntaxique abstrait (AST nodes) à la compilation
et produisent du code qui est ajouté dans un programme. Par exemple:

```crystal
macro define_method(name, content)
  def {{name}}
    {{content}}
  end
end

# Ce qui génére:
#
#     def foo
#       1
#     end
define_method foo, 1

foo #=> 1
```

Le corps de la définition d'une macro ressemble à du code Crystal classique
avec une syntaxe supplémentaire pour manipuler les noeuds AST.
Le code généré doit être du code Crystal valide, ce qui signifie par exemple
que vous ne pouvez générer un `def` sans le `end` correspondant,
ou une seule expression `when` d'un `case`.

## Visibilité

Les macros déclarées au plus haut niveau sont visibles depuis tout le programme.
Si une macro de haut niveau est marquée comme `private` elle est seulement accessible depuis ce fichier.

Elles peuvent aussi être définies dans des classes et modules, et sont visibles dans ces portées.
Les macros sont aussi recherchées dans la lignée des parents (super-classes et modules qui sont inclus).

Par exemple, un bloc à qui est passé un objet à utiliser comme receveur évoqué par `with ... yield`
peut accéder aux macros définies dans la lignée desparents de l'objet:

```crystal
class Foo
  macro emphasize(value)
    "***#{ {{value}} }***"
  end

  def yield_with_self
    with self yield
  end
end

Foo.new.yield_with_self { emphasize(10) } #=> "***10***"
```

Les macros définies dans des classes et modules peuvent être invoquées en dehors de ceux-ci également:

```crystal
class Foo
  macro emphasize(value)
    "***#{ {{value}} }***"
  end
end

Foo.emphasize(10) # => "***10***"
```

## Interpolation

On utilise `{{...}}` pour copier, ou interpoler, un noeud AST, comme dans l'exemple précédent.

Remarquez que le noeud est copié tel quel. Si dans l'exemple précédent un symbole est passé,
le code généré devient invalide:

```crystal
# Ce qui génére:
#
#     def :foo
#       1
#     end
define_method :foo, 1
```

Remarquez que `:foo` était le résultat de l'interpolation,
car c'est ce qui a été passé à la macro. Vous pouvez utiliser la méthode `ASTNode#id` dans ce cas,
où vous avez juste besoin d'un identifieur.

## Appels de Macro

Vous pouvez invoquer un **sous-ensemble fixé** de méthodes sur des noeuds AST à la compilation.
Ces méthodes sont documentées dans le module fictif [Crystal::Macros](http://crystal-lang.org/api/Crystal/Macros.html).

Par exemple, invoquer `ASTNode#id` dans l'exemple précédent résoud le problème:

```crystal
macro define_method(name, content)
  def {{name.id}}
    {{content}}
  end
end

# This correctly generates:
#
#     def foo
#       1
#     end
define_method :foo, 1
```

## Conditions

Vous utilisez `{% if condition %}` ... `{% end %}` pour générer conditionnellement du code:

```crystal
macro define_method(name, content)
  def {{name}}
    {% if content == 1 %}
      "one"
    {% else %}
      {{content}}
    {% end %}
  end
end

define_method foo, 1
define_method bar, 2

foo #=> one
bar #=> 2
```

Similaire à du code classique, `Nop`, `NilLiteral` et un `BoolLiteral` faux sont considérés *faux*,
alors que tout le reste est considéré comme *vrai*.

Les conditions de macro peuvent être utilisées en dehors de la définition de la macro:

```crystal
{% if env("TEST") %}
  puts "We are in test mode"
{% end %}
```

### Itération

Pour itérer sur un `ArrayLiteral`:

```crystal
macro define_dummy_methods(names)
  {% for name, index in names %}
    def {{name.id}}
      {{index}}
    end
  {% end %}
end

define_dummy_methods [foo, bar, baz]

foo #=> 0
bar #=> 1
baz #=> 2
```

La variable `index` dans l'exemple précédent est optionnelle.

Pour itérer sur un `HashLiteral`:

```crystal
macro define_dummy_methods(hash)
  {% for key, value in hash %}
    def {{key.id}}
      {{value}}
    end
  {% end %}
end
define_dummy_methods({foo: 10, bar: 20})
foo #=> 10
bar #=> 20
```

Les itérations de macro peuvent être utilisées en dehors de la définition d'une macro:

```crystal
{% for name, index in ["foo", "bar", "baz"] %}
  def {{name.id}}
    {{index}}
  end
{% end %}

foo #=> 0
bar #=> 1
baz #=> 2
```

## Arguments variables et splatting

Une macro peut accepter un nombre variable d'arguments:

```crystal
macro define_dummy_methods(*names)
  {% for name, index in names %}
    def {{name.id}}
      {{index}}
    end
  {% end %}
end

define_dummy_methods foo, bar, baz

foo #=> 0
bar #=> 1
baz #=> 2
```

Les arguments sont rassemblés en un `ArrayLiteral` et passés à la macro.

De plus, utiliser `*` lors de l'interpolation d'un `ArrayLiteral` interpole les éléments séparés par une virgule:

```crystal
macro println(*values)
   print {{*values}}, '\n'
end

println 1, 2, 3 # outputs 123\n
```

### Information de type

Quand une macro est invoquée vous pouvez accéder à la visibilité courante, ou type, avec une variable spéciale:
`@type`. Le type de cette variable est `TypeNode`, qui vous donne accès aux informations de type à la compilation.

Remarquez que `@type` est toujours le type *instance*,
même lorsque la macro est invoquée depuis une méthode de classe.

### Constantes

Les macros peuvent accéder aux constantes.
Par exemple:

```crystal
VALUES = [1, 2, 3]

{% for value in VALUES %}
  puts {{value}}
{% end %}
```

Si la constante dénote un type, vous obtenez en retour un `TypeNode`.
