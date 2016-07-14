# if var.nil?

Si la condition d'un `if` est `var.nil?` alors le type de `var` dans la branche `then`
est connu par le compilateur comme étant `Nil`,
et connu comme non-`Nil` dans la branche `else`:

```crystal
a = une_condition ? nil : 3
if a.nil?
  # ici a est Nil
else
  # ici a est Int32
end
```
