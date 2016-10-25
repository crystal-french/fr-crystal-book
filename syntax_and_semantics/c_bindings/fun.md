# fun

Une déclaration `fun` dans une `lib` se lie à une fonction C.

```crystal
lib C
  # En C: double cos(double x)
  fun cos(value : Float64) : Float64
end
```

Une fois liée, la fonction est disponible dans le type `C` comme si c'était une méthode de classe:

```crystal
C.cos(1.5) #=> 0.0707372
```

Vous pouvez omettre les parenthèses si la fonction n'a pas d'argument (et les omettre également lors de l'appel):

```crystal
lib C
  fun getch : Int32
end

C.getch
```

Si le type de retour est vide vous pouvez l'omettre:

```crystal
lib C
  fun srand(seed : UInt32)
end

C.srand(1_u32)
```

Vous pouvez lier une fonction aux arguments variables:

```crystal
lib X
  fun variadic(value : Int32, ...) : Int32
end

X.variadic(1, 2, 3, 4)
```

Remarquez qu'il n'y a pas de conversions implicites (exceptée `to_unsafe`, expliquée plus loin)
lors de l'appel à une fonction C: vous devez passer le type exact attendu.
Pour les entiers et les flottants vous pouvez utiliser les différentes méthodes `to_...`.

Parce-que les noms de méthodes dans Crystal doivent commencer par une lettre minuscule,
les noms des `fun` doivent aussi commencer par une lettre minuscule.
Si vous avez besoin de vous lier à une fonction C qui commence par une lettre capitale
vous pouvez lui donner un autre nom pour Crystal:

```crystal
lib LibSDL
  fun init = SDL_Init(flags : UInt32) : Int32
end
```

Vous pouvez aussi utiliser une string comme nom si le nom n'est pas un identifiant ou un nom de type valide:

```crystal
lib LLVMIntrinsics
  fun ceil_f32 = "llvm.ceil.f32"(value : Float32) : Float32
end
```

On peut aussi le faire pour donner des noms plus courts, plus sympas à des fonctions C, étant donné qu'ils ont tendance à être
longs et généralement préfixés par le nom de la librairie.

Les types valides à utiliser en liaison C sont:
* Les types primitifs (`Int8`, ..., `Int64`, `UInt8`, ..., `UInt64`, `Float32`, `Float64`)
* Les types de pointeur (`Pointer(Int32)`, qui peut aussi être écrit `Int32*`)
* Les arrays statiques (`StaticArray(Int32, 8)`, qui peut aussi être écrit `Int32[8]`)
* Les types de fonction (`Function(Int32, Int32)`, qui peut aussi être écrit `Int32 -> Int32`)
* Tout autre `struct`, `union`, `enum`, `type` ou `alias`déclarés précédemment.
* `Void`: l'absence de valeur retour.
* `NoReturn`: similaire à `Void`, mais le compilateur comprend que aucun code ne peut être invoqué après cette invocation.
* Les structs Crystal marquées avec l'attribut `@[Extern]`

Reportez-vous à la [grammaire de type](../type_grammar.html) pour les notations utilisées dans les types fun.

La librairie standard définit la librairie [LibC](https://github.com/crystal-lang/crystal/blob/master/src/lib_c.cr) avec des alias pour les types C communs,
comme `int`, `short`, `size_t`. Utilisez-les dans des liaisons ainsi:

```crystal
lib MyLib
  fun my_fun(some_size : LibC::SizeT)
end
```

**Note:** Le type C `char` est `UInt8` en Crystal, ainsi un `char*` ou un `const char*` est `UInt8*`.
Le type `Char` en Crystal est un codepoint unicode et est donc représenté sur quatre bytes,
le rendant similaire à un `Int32`, et non un `UInt8`. Il y a aussi l'alias `LibC::Char` en cas de doute.
