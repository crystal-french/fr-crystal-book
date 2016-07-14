# if ternaire

Le `if` ternaire permet d'écrire un `if` de manière plus succinte:

```crystal
a = 1 > 2 ? 3 : 4

# L'exemple précédent est équivalent à:
a = if 1 > 2
      3
    else
      4
    end
```
