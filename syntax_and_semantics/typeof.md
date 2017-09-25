# typeof

L'expression `typeof` retourne le type d'une expression:

```crystal
a = 1
b = typeof(a) #=> Int32
```

Elle accepte plusieurs arguments, et le résultat est l'union des types d'expression:

```crystal
typeof(1, "a", 'a') #=> (Int32 | String | Char)
```

Elle est souvent utilisée dans du code générique, pour profiter des capacités d'inférence de type du compilateur:

```crystal
hash = {} of Int32 => String
another_hash = typeof(hash).new #:: Hash(Int32, String)
```

Alors que `typeof` n'évalue en fait pas l'expression, on peut l'utiliser
sur des méthodes à la compilation, comme dans cet exemple, qui
forme récursivement une union de types à partir de paramètres de type imbriqué:

```crystal
class Array
  def self.elem_type(typ)
    if typ.is_a?(Array)
      elem_type(typ.first)
    else
      typ
    end
  end
end

nest = [1, ["b", [:c, ['d']]]]
flat = Array(typeof(Array.elem_type(nest))).new
typeof(nest) #=> Array(Int32 | Array(String | Array(Symbol | Array(Char))))
typeof(flat) #=> Array(String | Int32 | Symbol | Char)
```

Cette expression est également disponible dans la [grammaire de type](type_grammar.html).
