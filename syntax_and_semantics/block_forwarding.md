# Passage de bloc

Pour passer des blocs capturés, vous utilisez un argument de bloc, en préfixant une expression avec `&`:

```crystal
def capture(&block)
  block
end

def invoke(&block)
  block.call
end

proc = capture { puts "Hello" }
invoke(&proc) # prints "Hello"
```

Dans l'exemple précédent, `invoke` reçoit un bloc. On ne peut lui passer `proc` directement car `invoke`
ne reçoit pas les arguments classiques, seulement un argument bloc. Nous utilisons `&`
pour spécifier que nous voulons passer `proc` comme argument de bloc. Sinon:

```crystal
invoke(proc) # Error: wrong number of arguments for 'invoke' (1 for 0)
```

Vous pouvez en fait passer un proc à une méthode qui appelle yield:

```crystal
def capture(&block)
  block
end

def twice
  yield
  yield
end

proc = capture { puts "Hello" }
twice &proc
```

Ce qui précède est simplement réécrit en:

```crystal
proc = capture { puts "Hello" }
twice do
  proc.call
end
```

Ou, en combinant les syntaxes `&` et `->`:

```crystal
twice &->{ puts "Hello" }
```

Ou:

```crystal
def say_hello
  puts "Hello"
end

twice &->say_hello
```

## Passer des blocs non capturés

Pour passer des blocs non capturés, vous devez utiliser `yield`:

```crystal
def foo
  yield 1
end

def wrap_foo
  puts "Avant foo"
  foo do |x|
    yield x
  end
  puts "Après foo"
end

wrap_foo do |i|
  puts i
end

# Sortie:
# Avant foo
# 1
# Après foo
```

Vous pouvez aussi utiliser la syntaxe `&block` pour passer les blocs,
mais dans ce cas vous devez au moins spécifier les types en entrée,
et le code généré incluera des closures et sera plus lent:

```crystal
def foo
  yield 1
end

def wrap_foo(&block : Int32 -> _)
  puts "Avant foo"
  foo(&block)
  puts "Après foo"
end

wrap_foo do |i|
  puts i
end

# Sortie:
# Avant foo
# 1
# Après foo
```

Essayez d'éviter de passer des blocs de cette manière si utiliser `yield` suffit.
Il y a aussi le problème que `break` et `next` ne sont pas autorisés dans un bloc capturé,
ainsi ce qui suit ne fonctionnera pas en passant avec `&block`:

```crystal
foo_forward do |i|
  break # error
end
```

Pour faire court, éviter de passer avec `&block` quand `yield` est utilisé.
