# break

Vous pouvez utiliser `break` pour sortir d'une boucle `while`:

```crystal
a = 2
while (a += 1) < 20
  if a == 10
    # va à 'puts a'
    break
  end
end
puts a #=> 10
```
