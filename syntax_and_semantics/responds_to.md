# responds_to?

La pseudo-méthode `responds_to?` détermine si un type a une méthode qui répond à un nom. Par exemple:

```crystal
a = 1
a.responds_to?(:abs)    #=> true
a.responds_to?(:size) #=> false
```

C'est une pseudo-méthode car elle accepte un litéral symbole comme argument,
et est aussi traitée de manière spéciale par le compilateur, comme expliqué dans [if var.responds_to?(...)](if_varresponds_to.html).
