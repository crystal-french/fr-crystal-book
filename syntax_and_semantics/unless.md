# unless

Un `unless` évalue sa branche `then` si sa condition est *fausse*,et évalue la branche `else`,
si elle existe, sinon. Ainsi, elle fonctionne de manière inverse à `if`:

```crystal
unless une_condition
  alors_expression
else
  sinon_expression
end

# L'exemple précédent est équivalent à:
if une_condition
  alors_expression
else
  sinon_expression
end

# Peut aussi être écrit comme suffixe
fermer_porte unless porte_fermée?
```
