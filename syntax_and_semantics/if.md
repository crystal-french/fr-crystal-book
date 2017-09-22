# if

Un `if` évalue la branche `then` si sa condition est *vraie*. Sinon il évalue la branche `else`, si elle existe.

```crystal
a = 1
if a > 0
  a = 10
end
a #=> 10

b = 1
if b > 2
  b = 10
else
  b = 20
end
b #=> 20
```

Pour écrire des if-else-if imbriqués on utilise `elsif`:

```crystal
if une_condition
  faire_quelque_chose
elsif une_autre_condition
  faire_autre_chose
else
  faire_ça
end
```

Après un `if`, le type d'une variable dépend du type des expressions utilisées dans chaque branche.

```crystal
a = 1
if une_condition
  a = "hello"
else
  a = true
end
# a : String | Bool

b = 1
if une_condition
  b = "hello"
end
# b : Int32 | String

if une_condition
  c = 1
else
  c = "hello"
end
# c : Int32 | String

if une_condition
  d = 1
end
# d : Int32 | Nil
```

Remarquez que si une variable est déclarée dans une des branches et pas une autre,
à la fin du `if` elle incluera également le type `Nil`.

Dans une branche `if` le type d'une variable est celui assigné dans cette branche,
ou celui qu'elle avait avant d'être affectée:

```crystal
a = 1
if une_condition
  a = "hello"
  # a : String
  a.size
end
# a : String | Int32
```

Ainsi, le type d'une variable est le type de la dernière expression (ou dernières expressions)
qui lui est(sont) affectée(s).

Si une des branches n'est jamais atteinte à la fin d'un `if`, comme dans le cas d'un `return`, `next`, `break` ou `raise`, ce type n'est pas considéré à la fin du `if`:

```crystal
if une_condition
  e = 1
else
  e = "hello"
  # e : String
  return
end
# e : Int32
```
