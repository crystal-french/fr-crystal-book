# alias

En utilisant `alias` vous pouvez donner un nom différent à un type:

```crystal
alias PInt32 = Pointer(Int32)

ptr = PInt32.malloc(1) # : Pointer(Int32)
```

A chaque fois que vous utilisez un alias le compilateur le remplace par le type auquel il fait référence.

Les aliases sont utiles pour éviter d'écrire de longs noms de types, mais également pour pouvoir parler de types recursives:

```crystal
alias RecArray = Array(Int32) | Array(RecArray)

ary = [] of RecArray
ary.push [1, 2, 3]
ary.push ary
ary #=> [[1, 2, 3], [...]]
```

Un exemple réel de type récursif est json:

```crystal
module Json
  alias Type = Nil |
               Bool |
               Int64 |
               Float64 |
               String |
               Array(Type) |
               Hash(String, Type)
end
```
