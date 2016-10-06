# nil?

La pseudo-méthode `nil?` détermine si une expression est `Nil` à l'exécution. Par exemple:

```crystal
a = 1
a.nil?          # => false

b = nil
b.nil?          # => true
```

C'est une pseudo-méthode car le compilateur la connaît et peut affecter les informations de type,
comme expliqué dans [if var.nil?(...)](if_var_nil.html).

C'est équivalent à écrire `is_a?(Nil)` mais plus court et plus simple à lire et écrire.
