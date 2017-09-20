# enum

Une déclaration `enum` dans une `lib` déclare une enum C:

```crystal
lib X
  # En C:
  #
  #  enum SomeEnum {
  #    Zero,
  #    One,
  #    Two,
  #    Three,
  #  };
  enum SomeEnum
    Zero
    One
    Two
    Three
  end
end
```

Comme en C, le premier membre d'une enum a une valeur de zéro et chaque valeur successive est incrémentée de un.

Pour utiliser une valeur:

```crystal
X::SomeEnum::One #=> One
```

Vous pouvez spécifier la valeur d'un membre:

```crystal
lib X
  enum SomeEnum
    Ten = 10
    Twenty = 10 * 2
    ThirtyTwo = 1 << 5
  end
end
```

Comme vous pouvez le voir, des expressions mathématiques de base sont autorisées pour la valeur d'un membre: `+`, `-`, `*`, `/`, `&`, `|`, `<<`, `>>` et `%`.

Le type d'un membre enum est `Int32` par defaut, même si vous spécifiez un type différent dans une valeur constante:

```crystal
lib X
  enum SomeEnum
    A = 1_u32
  end
end

X::SomeEnum #=> 1_i32
```

Néanmoins, vous pouvez changer ce type par défaut:

```crystal
lib X
  enum SomeEnum : Int8
    Zero,
    Two = 2
  end
end

X::SomeEnum::Zero #=> 0_i8
X::SomeEnum::Two  #=> 2_i8
```

Vous pouvez utiliser une enum comme type dans un argument de `fun` ou des membres `struct` ou `union`:

```crystal
lib X
  enum SomeEnum
    One
    Two
  end

  fun some_fun(value : SomeEnum)
end
```
