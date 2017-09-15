# as?

La pseudo-méthode `as?` est équivalente à `as`,
excepté qu'elle retourne `nil` au lieu de relever une exception quand le type ne correspond pas.
Elle peut aussi être utilisée pour le transtypage entre des types de pointeur et d'autres types.

Exemple:

```crystal
value = rand < 0.5 ? -3 : nil
result = value.as?(Int32) || 10

value.as?(Int32).try &.abs
```
