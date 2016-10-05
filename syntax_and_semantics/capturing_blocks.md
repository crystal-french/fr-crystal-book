# Capture de blocs

Un bloc peut être capturé et converti en un `Proc`,
qui représente unn bloc de code associé à un contexte: les données de la clôture (closure).

Pour capturer un bloc vous devez le spécifier comme argument bloc d'une méthode,
donnez-lui un nom et spécifiez les types en entrée et en sortie. Par exemple:

```crystal
def int_to_int(&block : Int32 -> Int32)
  block
end

proc = int_to_int { |x| x + 1 }
proc.call(1) #=> 2
```

Le code précédent capture le bloc de code passé à `int_to_int` dans la variable de `block`,
et le renvoie depuis la méthode. Le type de `proc` est [Proc(Int32, Int32)](http://crystal-lang.org/api/Proc.html),
une fonction qui accepte un unique argument `Int32` et retourne un `Int32`.

De cette manière un bloc peut être sauvé en tant que fonction de rappel (callback):

```crystal
class Model
  def on_save(&block)
    @on_save_callback = block
  end

  def save
    if callback = @on_save_callback
      callback.call
    end
  end
end

model = Model.new
model.on_save { puts "Saved!" }
model.save # prints "Saved!"
```

Dans l'exemple précédent le type de `&block` n'était pas spécifié:
cela signifie juste que le bloc capturé n'a aucun argument et ne retourne rien.

Notez que si le type retourné n'est pas spécifié, rien n'est retourné par l'appel du proc:

```crystal
def some_proc(&block : Int32 ->)
  block
end

proc = some_proc { |x| x + 1 }
proc.call(1) # void
```

Pour avoir quelque chose en retour, soit on spécifie le type de retour ou on utilise un underscore pour permettre
tout type de retour:

```crystal
def some_proc(&block : Int32 -> _)
  block
end

proc = some_proc { |x| x + 1 }
proc.call(1) # 2

proc = some_proc { |x| x.to_s }
proc.call(1) # "1"
```

## break et next

`return` et `break` ne peuvent être utilisés dans un bloc capturé.
`next` peut être utilisé, provoquera un exit et donnera la valeur du bloc capturé.

## with ... yield

Le récepteur par défaut d'un bloc ne peut pas être modifié à l'aide de `with ... yield`.
