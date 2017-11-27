# Constantes

Les constantes peuvent être déclarées au plus haut niveau ou dans d'autres types.
Elles doivent débuter par une lettre majuscule:

```crystal
PI = 3.14

module Earth
  RADIUS = 6_371_000
end

PI #=> 3.14
Earth::RADIUS #=> 6_371_000
```

Bien que non imposé par le compilateur, les constantes sont généralement nommées avec seulement des lettres capitales et des caractères souligné pour séparer les mots.

Une définition de constante peut invoquer des méthodes à la logique complexe:

```crystal
TEN = begin
  a = 0
  while a < 10
    a += 1
  end
  a
end

TEN #=> 10
```
