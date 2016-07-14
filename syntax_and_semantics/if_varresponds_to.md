# if var.responds_to?(...)

Si une condition `if` est un test `responds_to?`, dans la branche `then`
le type de la variable est assuré d'être restreint aux types auxquels répond la méthode:

```crystal
if a.responds_to?(:abs)
  # ici le type de a sera réduit à ceux répondant à la méthode 'abs'
end
```

De plus, dans la branche `else` le type de la variable est assuré d'être restreint
aux types qui ne répondent pas à la méthode:

```crystal
a = une_condition ? 1 : "hello"
# a : Int32 | String

if a.responds_to?(:abs)
  # ici a sera Int32, étant donné que Int32#abs existe et pas String#abs
else
  # ici a sera une String
end
```

Ce qui précède ne fonctionne **pas** avec les variables d'instance,
les variables de classe ou les variables globales.
Pour fonctionner avec, assignez-les à une variable:

```crystal
if @a.responds_to?(:abs)
  # ici @a n'est pas assurée de répondre à `abs`
end

a = @a
if a.responds_to?(:abs)
  # ici a est assurée de répondre à `abs`
end

# Plus court:
if (a = @a).responds_to?(:abs)
  # ici a est assurée de répondre a `abs`
end
```
