# Attributs

Certains types et méthodes peuvent être annotés avec des attributs.
La liste d'attributs est fixe, mais éventuellement il y aura la possibilité d'avoir des attributs définis par l'utilisateur.

## Link

Dit au compilateur comment lier une librairie C.
Ceci est expliqué dans la section [lib](c_bindings/lib.html).

## Extern

Le marquage d'une structure Crystal avec cet attribut permet de l'utiliser dans les déclarations lib:

```crystal
@[Extern]
struct MyStruct
end

lib MyLib
  fun my_func(s : MyStruct) # OK (renvoie une erreur sans l'attribut Extern)
end
```

Vous pouvez également créer une structure comme une union C (cela peut être très dangereux):

```crystal
# Une structure pour convertir facilement entre les points de code Int32 et Chars
@[Extern(union: true)]
struct Int32OrChar
  property int = 0
  property char = '\0'
end

s = Int32OrChar.new
s.char = 'A'
s.int # => 65

s.int = 66
s.char # => 'B'
```

## ThreadLocal

L'attribut `@[ThreadLocal]` peut être appliqué à des variables globales et des variables de classe.
Cela en fait un thread local.

```crystal
class NePasUtiliser
  # Une pour chaque thread
  @[ThreadLocal]
  $values = [] of Int32
end
```

## Packed

Permet de marquer une[struct C](c_bindings/struct.html) comme empaquetée,
fait aligner la struct sur un byte, et qu'il n'y a pas de padding entre les éléments.
Dans les structs non empaquetées, un padding entre les types de champs est inséré selon le système cible.

## AlwaysInline

Indique au compilateur de toujours aligner une méthode:

```crystal
@[AlwaysInline]
def foo
  1
end
```

## NoInline

Dit au compilateur de ne jamais aligner un appel de méthode.
Cela n'aucun effet si la méthode yielde.

```crystal
@[NoInline]
def foo
  1
end
```

## ReturnsTwice

Marque une méthode ou une [lib fun](c_bindings/fun.html) pour faire deux fois son retour.
La fonction C `setjmp` est un exemple d'une telle fonction.

## Raises

Marque une méthode ou [lib fun](c_bindings/fun.html) comme pouvant potentiellement lever une exception.
Ceci est expliqué dans la section [callbacks](c_bindings/callbacks.html).

## CallConvention

Indique la convention d'appel d'une [lib fun](c_bindings/fun.html). Par exemple:

```crystal
lib LibFoo
  @[CallConvention("X86_StdCall")]
  fun foo : Int32
end
```

La liste de conventions d'appel valides est:

* C (le défaut)
* Fast
* Cold
* WebKit_JS
* AnyReg
* X86_StdCall
* X86_FastCall

Elles sont expliquées [ici](http://llvm.org/docs/LangRef.html#calling-conventions).

## Flags

Marque un [enum](enum.html) comme "flags enum", qui change le comportement de certaines de ses méthodes, comme `to_s`.
