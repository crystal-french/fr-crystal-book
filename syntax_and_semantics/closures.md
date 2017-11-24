# Closures

Les blocs capturés et les litéraux proc sont des closures pour les variables locales et `self`.
Un exemple sera plus révélateur:

```crystal
x = 0
proc = ->{ x += 1; x }
proc.call #=> 1
proc.call #=> 2
x         #=> 2
```

Ou avec un proc renvoyé depuis une méthode:

```crystal
def counter
  x = 0
  ->{ x += 1; x }
end

proc = counter
proc.call #=> 1
proc.call #=> 2
```

Dans l'exemple précédent, bien que `x` est une variable locale, elle a été capturée par le litéral proc.
Dans ce cas le compilateur alloue `x` sur le tas et l'utilise comme contexte de données du proc pour le faire fonctionner,
car normalement les variables locales existent sur la pile et disparaissent après le retour d'une méthode.

## Type de variables de closure

Le compilateur fait généralement plutôt preuve d'intelligence sur le type des variables locales. Par exemple:

```crystal
def foo
  yield
end

x = 1
foo do
  x = "hello"
end
x # : Int32 | String
```

Le compilateur sait que après le bloc, `x` peut être de type Int32 ou String
(il pourrait savoir qu'il serait toujours de type String car la méthode yields toujours, ce qui sera peut-être amélioré à l'avenir).

Si `x` est à nouveau affecté après le bloc, le compilateur sait que le type a changé:

```crystal
x = 1
foo do
  x = "hello"
end
x # : Int32 | String

x = 'a'
x # : Char
```

Néanmoins, si `x` est dans une closure via le proc, le type est toujours la somme des types de toutes ses affectations:

```crystal
def capture(&block)
  block
end

x = 1
capture { x = "hello" }

x = 'a'
x # : Int32 | String | Char
```

C'est parce-que le bloc capturé pourrait avoir été stocké dans une variable globale, de classe ou d'instance et invoqué depuis un autre thread entre chaque instruction.
Le compilateur ne fait pas une analyse exhaustive de cela: il assume juste que si la variable est capturée par un proc, le temps d'invocation de ce proc est inconnu.

Cela arrive également avec les litéraux de proc habituels, même si il est évident que le proc n'ait été invoqué ou stocké:

```crystal
def capture(&block)
  block
end

x = 1
->{ x = "hello" }

x = 'a'
x # : Int32 | String | Char
```
