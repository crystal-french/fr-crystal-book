# && - Loperateur logique ET

Un `&&` (ET) évalue sa partie gauche. Si elle est *vraie*,
elle évalue sa partie droie et prend cette valeur. Sinon, elle a la valeur de la partie gauche.
Son type est l'union des types des deux côtés.

Vous pouvez voir le `&&` comme sucre syntaxique d'un `if`:

```crystal
some_exp1 && some_exp2

# L'exemple précédent est équivalent à:
tmp = some_exp1
if tmp
  some_exp2
else
  tmp
end
```
