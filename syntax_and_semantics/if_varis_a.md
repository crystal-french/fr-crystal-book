# if var.is_a?(...)

Si la condition d'un `if` est un test `is_a?`,
le type d'une variable est assuré d'être restreint à ce type
dans la branche `then`.

```crystal
if a.is_a?(String)
  # ici a est une String
end

if b.is_a?(Number)
  # ici b est un Number
end
```

De plus, dans la branche `else` le type de la variable est assuré
pour ne pas être restreint à ce type:

```crystal
a = une_condition ? 1 : "hello"
# a : Int32 | String

if a.is_a?(Number)
  # a : Int32
else
  # a : String
end
```

Notez que vous pouvez utiliser tout type dans un test `is_a?`, comme des classes abstraites ou des modules.

Ce qui précède fonctionne également s'il y a un ET logique (`&&`) dans la condition:

```crystal
if a.is_a?(String) && b.is_a?(Number)
  # ici a est une String et b est un Number
end
```

Ce qui précède ne fonctionne **pas** avec les variables d'instance, les variables de classe et les variables globales.
Pour que cela fonctionne avec, commencez par les affecter à une variable:

```crystal
if @a.is_a?(String)
  # ici @a n'est pas assurée d'être une String
end

a = @a
if a.is_a?(String)
  # ici a est assurée d'être une String
end

# Plus succint:
if (a = @a).is_a?(String)
  # ici a est assurée d'être une String
end
```
