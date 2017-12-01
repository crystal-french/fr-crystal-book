# Hooks

Des macros spéciales existent qui sont invoquées dans certaines situations, comme des hooks: `inherited`, `included`, `extended` et `method_missing`.
* `inherited` est invoquée à la compilation quand une sous-classe est définie. `@type` est le type de l'héritage.
* `included` est invoquée à la compilation quand un module est inclus. `@type` est le type de l'inclusion.
* `extended` est invoquée à la compilation quand un module est étendu. `@type` est le type de l'extension.
* `method_missing` est invoquée à la ocmpilation quand une méthode n'est pas trouvée.

Exemple pour `inherited`:

```crystal
class Parent
  macro inherited
    def lineage
      "{{@type.name.id}} < Parent"
    end
  end
end

class Child < Parent
end

Child.new.lineage #=> "Child < Parent"
```

Exemple pour `method_missing`:

```crystal
macro method_missing(call)
  print "Got ", {{call.name.id.stringify}}, " with ", {{call.args.size}}, " arguments", '\n'
end

foo          # Prints: Got foo with 0 arguments
bar 'a', 'b' # Prints: Got bar with 2 arguments
```
