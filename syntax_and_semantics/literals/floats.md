# Float

Il y a deux types de nombre flottant, [Float32](http://crystal-lang.org/api/Float32.html) et [Float64](http://crystal-lang.org/api/Float64.html), qui correspondent au format simple précision (32 bits) et au format double précision (64 bits) comme définis par l'[IEEE](https://fr.wikipedia.org/wiki/IEEE_754).

Un litéral d'un flottant est un sign optionnel `+` ou `-` sign,
suivi d'une séquence de nombres ou soulignés, suivis par un point,
suivis par des nombres ou soulignés, suivis par un exposant optionnel,
suivis par un suffix de type optionnel. Sans suffix, le type du
litéral est `Float64`.

```crystal
1.0      # Float64
1.0_f32  # Float32
1_f32    # Float32

1e10     # Float64
1.5e10   # Float64
1.5e-7   # Float64

+1.3     # Float64
-0.5     # Float64
```

Le souligné `_` avant le suffixe est optionel.

Les soulignés peuvent être utilisés pour rendre certains nombres plus lisibles:

```crystal
1_000_000.111_111 # plus lisible que 1000000.111111, cependant la valeur reste la même
```
