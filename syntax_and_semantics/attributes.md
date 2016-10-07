# Attributs

Certains types et méthodes peuvent être annotés avec des attributs.
La liste d'attributs est fixe, mais éventuellement il y aura la possibilité d'avoir des attributs définis par l'utilisateur.

## Link

Dis au compilateur comment lier une librairie C.
Ceci est expliqué dans la section [lib](c_bindings/lib.html).

## ThreadLocal

L'attribut `@[ThreadLocal]` peut être appliqué à des variables globales et des variables de classe.
Cela en fait un thread local.

```crystal
# One for each thread
@[ThreadLocal]
$values = [] of Int32
```

## Packed

Permet de marqué une[struct C](c_bindings/struct.html) comme empaquetée,
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

Dis au compilateur de ne jamais aligner un appel de méthode.
Cela n'aucun effet si la méthode yielde.

```crystal
@[NoInline]
def foo
  1
end
```

## ReturnsTwice

Marque une méthode ou une [lib fun](c_bindings/fun.html) faire deux fois son retour. La fonction C `setjmp` est un exemple d'une telle fonction.

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
