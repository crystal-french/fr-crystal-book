# next

Vous pouvez utiliser `next` pour essayer de passer à l'exécution de l'itération suivante d'une boucle `while`.
Après l'exécution du `next`, la condition du `while` est vérifiée, et si *vraie*, le corps sera exécuté.

```crystal
a = 1
while a < 5
  a += 1
  if a == 3
    next
  end
  puts a
end
# Seront affichés les nombres 2, 4 et 5
```
