# until

Un `until` exécute son corps tant que sa condition est *vraie*.
Un `until` est simplement du sucre syntaxique pour un `while` avec une négation de la condition:

```crystal
until une_condition
  faire_cela
end

# L'exemple précédent est équivalent à:
while !une_condition
  faire_cela
end
```

`break` et `next` peuvent aussi être utilisés dans un `until`.
