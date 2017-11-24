# Callbacks

Vous pouvez utiliser des types fonction dans vos déclarations C:

```crystal
lib X
  # In C:
  #
  #    void callback(int (*f)(int));
  fun callback(f : Int32 -> Int32)
end
```

Vous pouvez ensuite passer une fonction (un [Proc](http://crystal-lang.org/api/Proc.html)) ainsi:

```crystal
f = ->(x : Int32) { x + 1 }
X.callback(f)
```

Si vous définissez la fonction comme inline dans le même appel vous pouvez omettre les types des arguments,
le compilateur ajoutera pour vous les types en se basant sur la signature de `fun`:

```crystal
X.callback ->(x) { x + 1 }
```

Remarquez, néanmoins, que les fonctions passées au C ne peuvent former des closures.
Si le compilateur détecte à la compilation qu'une closure est passée, une erreur sera renvoyée:

```crystal
y = 2
X.callback ->(x) { x + y } # Error: can't send closure
                           # to C function
```

Si le compilateur ne peut détecter ça à la compilation, une exception sera levée à l'exécution.

Reportez-vous à la [grammaire de type](../type_grammar.html) pour la notation utilisée dans les types callbacks et procs.

Si vous voulez passer `NULL` au lieu d'un callback, passez simplement `nil`:

```crystal
# Same as callback(NULL) in C
X.callback nil
```

### Passer une closure à une fonction C

La plupart du temps une fonction C qui permet de définir un callback fournit aussi un argument pour des données personnalisées.
Ces données personnalisées sont ensuite envoyées comme argument au callback.
Par exemple, supposez qu'une fonction C qui invoque un callback à chaque tic d'horloge, en passant ce tic d'horloge:

```crystal
lib LibTicker
  fun on_tick(callback : (Int32, Void* ->), data : Void*)
end
```

Pour définir proprement un wrapper pour cette fonction nous devons envoyer le Proc comme données du callback, puis convertir ces données de callback au Proc et finalement l'invoquer.

```crystal
module Ticker
  # Le callback pour l'utilisateur n'a pas de Void*
  def self.on_tick(&callback : Int32 ->)
    # Etant donné que Proc est un {Void*, Void*}, on ne peut le transformer en Void*,
    # alors nous l'"empaquetons": on alloue de la mémoire et on y stocke le Proc
    boxed_data = Box.box(callback)

    # Nous devons enregistrer ça dans l'espace Crystal afin que le ramasse-miettes ne le rammasse pas (*)
    @@box = boxed_data


    # Nous passons un callback qui ne forme pas de closure, et passons la boxed_data
    # comme données du callback
    LibTicker.on_tick(->(tick, data) {
      # Maintenant nous transformons les données en Proc, en utilisant Box.unbox
      data_as_callback = Box(typeof(callback)).unbox(data)
      # Et on invoque au final le callback utilisateur
      data_as_callback.call(tick)
    }, boxed_data)
  end
end

Ticker.on_tick do |tick|
  puts tick
end
```

Remarquez que nous sauvons le callback dans `@@box`.
La raison est que si nous ne le faisons pas, et que notre code n'y fait plus référence, le ramasse-miettes va le récupérer.
La librairie C va bien sûr stocker le callback, mais le ramasse-miettes de Crystal n'a aucun moyen de le savoir.

## Attribut Raises

Si une fonction C exécute un callback utilisateur qui peut lever une exception,
elle doit être annotée avec l'attribut `@[Raises]`.

Le compilateur infére cet attribut en tant que méthode s'il invoque une méthode qui est marquée comme `@[Raises]` ou qui lève une exception (récursivement).

Néanmoins, certaines fonctions C acceptent des callbacks qui sont exécutés par des fonctions C.
Par exemple, prenons une librairie fictive:

```crystal
lib LibFoo
  fun store_callback(callback : ->)
  fun execute_callback
end

LibFoo.store_callback ->{ raise "OH NO!" }
LibFoo.execute_callback
```

Si le callback passé à `store_callback` lève une exception, alors `execute_callback` en lévera une aussi.
Néanmoins, le compilateur ne sait pas que `execute_callback` peut potentiellement lever une exception car il n'est pas marqué avec `@[Raises]` et le compilateur n'a aucun moyen de le savoir. Dans ce cas, vous devez marquer manuellement de telles fonctions:

```crystal
lib LibFoo
  fun store_callback(callback : ->)

  @[Raises]
  fun execute_callback
end
```

Si vous ne les marquez pas, les blocs `begin/rescue` délimitant les appels de cette fonction n'auront pas le résultat attendu.
