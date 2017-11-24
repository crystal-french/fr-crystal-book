# || - Opérateur OU Logique

Un `||` (ou) évalue sa partie gauche.
Si elle est *fausse*, elle évalue sa partie droite et prend cette valeur.
Sinon elle a la valeur de la partie gauche.
Son type est l'union des types de chaque côté.

Vous pouvez voir le `||` comme le sucre syntaxique d'un `if`:

```crystal
some_exp1 || some_exp2

# L'exemple précédent est équivalent à:
tmp = some_exp1
if tmp
  tmp
else
  some_exp2
end
```
