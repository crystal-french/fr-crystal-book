# as

La pseudo-méthode `as` limite les types d'une expression. Par exemple:

```crystal
if some_condition
  a = 1
else
  a = "hello"
end

# a : Int32 | String
```

Dans le code précédent, `a` est l'union de `Int32 | String`.
Si pour une quelconque raison nous sommes sûr que `a` est un `Int32` après le `if`,
on peut forcer le compilateur pour le traiter comme tel:

```crystal
a_as_int = a.as(Int32)
a_as_int.abs          # works, compiler knows that a_as_int is Int32
```

La pseudo-méthode `as` effectue une vérification à l'exécution:
Si `a` n'était pas un `Int32`, une [exception](exception_handling.html) est levée.

L'argument de l'expression est un [type](type_grammar.html).

Il est impossible pour un type d'être limité à un autre type, une erreur à la compilation est retournée:

```crystal
1.as(String) # Compile-time error
```

**Note: ** vous ne pouvez utiliser `as` pour convertir un type en un type qui n'a aucun rapport:
`as` est comme un `cast` dans les autres langages. Les méthodes sur les entiers,
flottants et charactères sont fournies pour ces conversions. Alternativement, utilisez le typage de pointeur
comme expliqué ci-dessous.

## Conversion entre types de pointeur

La pseudo-méthode `as` permet également de transtyper entre types de pointeur:

```crystal
ptr = Pointer(Int32).malloc(1)
ptr.as(Int8*)                    #:: Pointer(Int8)
```

Dans ce cas, aucune vérification à l'exécution n'est faite:
les pointeurs sont dangereux (unsafe) et cette forme de typage est généralement utilisée seulement pour les liaisons C et le code bas-niveau.

## Conversion entre types de pointeur et autres types

La conversion entre types de pointeur et types Référence est également possible:

```crystal
array = [1, 2, 3]

# object_id returns the address of an object in memory,
# so we create a pointer with that address
ptr = Pointer(Void).new(array.object_id)

# Now we cast that pointer to the same type, and
# we should get the same value
array2 = ptr.as(Array(Int32))
array2.same?(array) #=> true
```

Aucune vérification à la compilation n'est faite ici car, à nouveau, des pointeurs sont utilisés.
L'utilité de ce typage est encore plus rare que pour le précédent, mai spermet d'implémenter certains types centraux (comme String)
dans Crystal lui-même, et permet également de passer un type Référence à des foncitons C en le transtypant vers un pointeur vide.

## Utilisation du transtypage pour un type plus important

La pseudo-méthode `as` peut-être utilisée pour transtyper une expression en un type "plus important".
Par exemple:

```crystal
a = 1
b = a.as(Int32 | Float64)
b #:: Int32 | Float64
```

Ce qui précéde peut sembler avoir une utilisation limitée, mais elle est pourtant utile, par exemple, lors de la correspondance d'un tableau d'éléments:

```crystal
ary = [1, 2, 3]

# We want to create an array 1, 2, 3 of Int32 | Float64
ary2 = ary.map { |x| x.as(Int32 | Float64) }

ary2 #:: Array(Int32 | Float64)
ary2 << 1.5 # OK
```

La méthode `Array#map` utilise le type de bloc comme type générique lpour l'Array.
Sans la pŝeudo-méthode `as`, le type inféré aurait été `Int32` et n'aurait pas été capable d'y ajouter `Float64`.

## Utilisation lorsque le compilateur ne peut inférer le type d'un bloc

Des fois le compilateur ne peut inférer le type d'un bloc. Ceci peut arriver dans des appels récursifs qui dépendent les uns des autres.
Dans ce cas vous pouvez utiliser `as` pour lui faire connaître le type:

```crystal
some_call { |v| v.method.as(ExpectedType) }
```
