# Proc

Un [Proc](http://crystal-lang.org/api/Proc.html) représente un pointeur de function avec un éventuel contexte (les données de la closure).
Il est typiquement créé avec un litéral de proc:

```crystal
# Un proc sans argument
->{ 1 } # Proc(Int32)

# Un proc avec un argument
->(x : Int32) { x.to_s } # Proc(Int32, String)

# Un proc avec deux arguments:
->(x : Int32, y : Int32) { x + y } # Proc(Int32, Int32, Int32)
```

Les types des arguments sont obligatoires, excepté quand on passe directement un litéral de proc à une `fun` d'une librairie 
dans un binding C.

Le type de retour est déduit du corps du proc.

Une méthode spéciale `new` est également disponible:

```crystal
Proc(Int32, String).new { |x| x.to_s } # Proc(Int32, String)
```

Cette forme vous permet de spécifier le type de retour est de le vérifier
par rapport au corps du proc.

## Le type Proc

Pour définir un type Proc vous pouvez utiliser:

```crystal
# Un Proc acceptant un unique argument Int32 et retournant une String
Proc(Int32, String)

# Un Proc n'acceptant aucun argument et returnant Void
Proc(Void)

# Un Proc acceptant 2 arguments (un Int32 et une String) et returnant un Char
Proc(Int32, String, Char)
```

Dans les restrictions de type, les arguments de type générique et autres endroits où un type est attendu,
vous pouvez utiliser une syntaxe succinte, comme expliqué dans la section [type](../type_grammar.html):

```crystal
# Un tableau de Proc(Int32, String, Char)
Array(Int32, String -> Char)
```

## Invocation

Pour invoquer un Proc, vous invoquez la méthode `call` dessus.
Le nombre d'arguments doit correspondre au type du proc:

```crystal
proc = ->(x : Int32, y : Int32) { x + y }
proc.call(1, 2) #=> 3
```

## Depuis des méthodes

Un Proc peut être créé à partir d'une méthode existante:

```crystal
def one
  1
end

proc = ->one
proc.call #=> 1
```

Si la méthode a des arguments, vous devez spécifier leurs types:

```crystal
def plus_one(x)
  x + 1
end

proc = ->plus_one(Int32)
proc.call(41) #=> 42
```

Un proc peut éventuellement spécifier un récepteur:

```crystal
str = "hello"
proc = ->str.count(Char)
proc.call('e') #=> 1
proc.call('l') #=> 2
```
