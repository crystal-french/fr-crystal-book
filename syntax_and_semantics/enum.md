# Enums

Un enum est un ensemble de valeurs entières, pour lesquelles chaque valeur est associée à un nom.
Par exemple:

```crystal
enum Color
  Red
  Green
  Blue
end
```

Un enum est défini à l'aide du mot clé `enum`, suivi de son nom. Le corps du enum contient les valeurs.
Les valeurs commencent à la valeur `0` et son incrémentées de un. La valeur par défaut peut être changée:

```crystal
enum Color
  Red         # 0
  Green       # 1
  Blue   = 5  # changée pour 5
  Yellow      # 6 (5 + 1)
end
```

Chaque constante d'un enum a le type de l'enum:

```crystal
Color::Red #:: Color
```

Pour obtenir la valeur on invoque `value`:

```crystal
Color::Green.value #=> 1
```

Le type de la valeur est `Int32` par défaut mais peut être changé:

```crystal
enum Color : UInt8
  Red
  Green
  Blue
end

Color::Red.value #:: UInt8
```

Seuls les différents types d'entier sont permis comme type.

Tout enum hérite de [Enum](http://crystal-lang.org/api/Enum.html).

## Flags enums

Un enum peut être marqué avec l'attribut `@[Flags]`. Cela modifie les valeurs par défaut:

```crystal
@[Flags]
enum IOMode
  Read # 1
  Write  # 2
  Async # 4
end
```

L'attribut `@[Flags]` définit la première valeur de constante à `1`, et les constantes successives sont multipliées par `2`.

Les constantes implicites, `None` et `All`, sont automatiquement ajoutées à ces enums,
où `None` a la valeur `0` et `All` a pour valeur le ou logique des constantes.

```crystal
IOMode::None.value #=> 0
IOMode::All.value  #=> 7
```

De plus, certaines méthodes d'`Enum` vérifient la méthode `@[Flags]`. Par exemple:

```crystal
puts(Color::Red)                    # prints "Red"
puts(IOMode::Write | IOMode::Async) # prints "Write, Async"
```

## Enums depuis des entiers

Un enum peut être créé depuis un entier:

```crystal
puts Color.new(1) #=> prints "Green"
```

Les valeurs qui ne correspondent pas aux constantes d'un enum sont autorisées:
les valeurs seront toujours de type `Color`, mais lorsqu'elles seront affichées vous obtiendrez la valeur sous-jacente:

```crystal
puts Color.new(10) #=> prints "10"
```

Cette méthode est surtout prévuee pour convertir des entiers en C vers des enums en Crystal.

## Méthodes

Tout comme une classe ou un struct, vous pouvez définir des méthodes pour des enums:

```crystal
enum Color
  Red
  Green
  Blue

  def red?
    self == Color::Red
  end
end

Color::Red.red?  #=> true
Color::Blue.red? #=> false
```

Les variables de classes sont autorisées, mais pas les variables d'instance.

## Usage

Les Enums sont des alternatives de sûreté du typage au [Symbol](http://crystal-lang.org/api/Symbol.html).
Par exemple, une méthode d'API peut spécifier une [restriction de type](type_restrictions.html) en utilisant un type enum:

```crystal
def paint(color : Color)
  case color
  when Color::Red
    # ...
  else
    # Rare, mais peut quand même se produire
    raise "unknown color: #{color}"
  end
end

paint Color::Red
```

Ce qui précède peut aussi être implémenté avec un Symbol:

```crystal
def paint(color : Symbol)
  case color
  when :red
    # ...
  else
    raise "unknown color: #{color}"
  end
end

paint :red
```

Néanmoins, si le développeur est coupable d'une typo, disons `:reed`, l'erreur se produira seulement à l'exécution, mais en écrivant `Color::Reed`
il obtiendra une erreur à la compilation.

Il est recommandé d'utiliser les enums autant que possible, d'utiliser les symbôles seulement pour l'implémentation interne d'une API,
et d'éviter les symbôles pour les APIs publiques. Mais vous êtes libre de faire comme bon vous semble.
