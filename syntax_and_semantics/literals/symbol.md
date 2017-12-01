# Symbole

Un [Symbole](http://crystal-lang.org/api/Symbol.html) est une constante identifiée par un nom sans que vous ayez à lui donner une valeur numérique.

```crystal
:hello
:good_bye

# Avec des espaces
:"symbol with spaces"

# Termine par un point d'interrogation ou d'exclamation
:question?
:exclamation!

# Pour les opérateurs
:+
:-
:*
:/
:==
:<
:<=
:>
:>=
:!
:!=
:=~
:!~
:&
:|
:^
:~
:**
:>>
:<<
:%
:[]
:[]?
:[]=
:<=>
:===
```

En interne, un symbole est représenté par un `Int32`.
