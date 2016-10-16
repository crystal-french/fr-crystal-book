# Drapeaux de compilation

Types, méthodes et généralement toute partie de votre code peuvent être définis conditionnellement suivant des drapeaux
disponibles à la compilation. Ces drapeaux sont par défaut lus depuis la [cible triple LLVM](http://llvm.org/docs/LangRef.html#target-triple) d'hôte,
séparés par des `-`. Pour obtenir la cible vous pouvez exécuter `llvm-config --host-target`.

```bash
$ llvm-config --host-target
x86_64-unknown-linux-gnu

# ainsi les drapeaux sont: x86_64, unknown, linux, gnu
```

De plus, si un programme est compilé avec `--release`, le drapeau `release` sera positionné.

Vous pouvez vérifier ces drapeaux avec la méthode macro `flag?`:

```crystal
{% if flag?(:x86_64) %}
  # du code spécifique au plate-forme 64 bits
{% else %}
  # du code spécifique au plate-forme non 64 bits
{% end %}
```

Elle retourne true ou false, vous pouvez donc utiliser `&&` et `||` normalement:

```crystal
{% if flag?(:linux) && flag?(:x86_64) %}
  # du code spécifique à linux 64 bits
{% end %}
```

Ces drapeaux sont généralement utilisés dans les liaisons C pour définir de manière conditionnelle des types et fonctions.
Par exemple le type bien connu `size_t` est défini comme suit en Crystal:

```crystal
lib C
  {% if flag?(:x86_64) %}
    alias SizeT = UInt64
  {% else %}
    alias SizeT = UInt32
  {% end %}
end
```
