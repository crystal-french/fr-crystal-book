# Comme expression

La valeur d'un `if` est la valeur de la dernière expression trouvée dans les branches:

```crystal
a = if 2 > 1
      3
    else
      4
    end
a #=> 3
```

Si une branche d'un `if` est vide, ou manquante,
elle est considérée comme ayant `nil`:

```crystal
if 1 > 2
  3
end

# L'exemple précédent est équivalent à:
if 1 > 2
  3
else
  nil
end

# Un autre exemple:
if 1 > 2
else
  3
end

# L'exemple précédent est équivalent à:
if 1 > 2
  nil
else
  3
end
```
