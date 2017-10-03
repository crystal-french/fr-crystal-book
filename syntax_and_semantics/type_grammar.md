# Grammaire de type

Quand:

* vous spécifiez des [restrictions de type](type_restrictions.html)
* vous spécifiez des [arguments de type](generics.html)
* vous déclarez des [variables](declare_var.html)
* vous déclarez des [aliases](alias.html)
* vous déclarez des [typedefs](c_bindings/type.html)
* pour l'argument d'un pseudo-appel à [is_a?](is_a.html)
* pour l'argument d'une expression [as](as.html)
* pour l'argument d'une expression [sizeof](sizeof.html)
* pour l'argument d'une expression [instance_sizeof](instance_sizeof.html)
* pour le [type de retour](return_types.html) d'une méthode

Une syntaxe pratique est fournie pour certains types communs.
Elles sont surtout utiles lors de l'écriture de [liaisons C](c_bindings/index.html),
mais peuvent être utilisées depuis n'importe lequel des emplacements précédents.

## Chemins et génériques:

Les génériques et les types usuels peuvent être utilisés:

```crystal
Int32
My::Nested::Type
Array(String)
```

## Union

```crystal
alias Int32OrString = Int32 | String
```

Le tube (`|`) dans les types crée un type union. `Int32 | String` se lit "Int32 ou String".
Dans du code usuel, `Int32 | String` signifie invoquer la méthode `|` sur `Int32` avec `String` comme argument.

## Nilable

```crystal
alias Int32OrNil = Int32?
```

Est équivalent à:

```crystal
alias Int32OrNil = Int32 | ::Nil
```

Dans du code usuel, `Int32?` est une erreur de syntaxe.

## Pointeur

```crystal
alias Int32Ptr = Int32*
```

Est équivalent à:

```crystal
alias Int32Ptr = Pointer(Int32)
```

Dans du code usuel, `Int32*` signifie invoquer la méthode `*` sur `Int32`.

## StaticArray

```crystal
alias Int32_8 = Int32[8]
```

Est équivalent à:

```crystal
alias Int32_8 = StaticArray(Int32, 8)
```

Dans du code usuel, `Int32[8]` signifie invoquer la méthode `[]` sur `Int32` avec `8` comme argument.

## Tuple

```crystal
alias Int32StringTuple = {Int32, String}
```

Est équivalent à:

```crystal
alias Int32StringTuple = Tuple(Int32, String)
```

Dans du code usuel, `{Int32, String}` est une instance de tuple contenant `Int32` et `String` comme éléments.
C'est différent du **type** tuple précédent.

## NamedTuple

```crystal
alias Int32StringNamedTuple = {x: Int32, y: String}
```

Est équivalent à:

```crystal
alias Int32StringNamedTuple = NamedTuple(x: Int32, y: String)
```

Dans du code usuel, `{x: Int32, y: String}` est une instance de tuple nommé contenant `Int32` et `String` pour `x` et `y`.
C'est différent du **type** de tuple nommé précédent.

## Proc

```crystal
alias Int32ToString = Int32 -> String
```

Est équivalent à:

```crystal
alias Int32ToString = Proc(Int32, String)
```

Pour spécifier un Proc sans argument:

```crystal
alias ProcThatReturnsInt32 = -> Int32
```

Pour spécifier plusieurs arguments:

```crystal
alias Int32AndCharToString = Int32, Char -> String
```

Pour des procs imbriqués (et pour tout type en général), vous pouvez utiliser des parenthèses:

```crystal
alias ComplexProc = (Int32 -> Int32) -> String
```

Dans du code usuel `Int32 -> String` est une erreur de syntaxe.

## self

`self` peut être utilisé dans la grammaire de type pour dénoter un type `self`.
Reportez-vous à la section sur les [restrictions de type](type_restrictions.html).

## class

`class` est utilisée pour se référer à un type de classe, au lieu d'un type d'instance.

Par exemple:

```crystal
def foo(x : Int32)
  "instance"
end

def foo(x : Int32.class)
  "class"
end

foo 1     # "instance"
foo Int32 # "class"
```

`class` est aussi utile pour créer des arrays et collections de type de classe:

```crystal
class Parent
end

class Child1 < Parent
end

class Child2 < Parent
end

ary = [] of Parent.class
ary << Child1
ary << Child2
```

## Underscore

Un souligné (tiret bas) n'est permis que dans les restrictions de type. Il effectue
une correspondance avec n'importe quoi:

```crystal
# équivalent à spécifier une restriction, pas très utile
def foo(x : _)
end

# Un petit peu plus utile: deux Procs arguments qui retournent un Int32:
def foo(x : _, _ -> Int32)
end
```

## typeof

`typeof` est permis dans la grammaire de type.
Il retourne un type d'union du type des expressions passées:

```crystal
alias SameAsInt32 = typeof(1 + 2)
alias Int32OrString = typeof(1, "a")
```
