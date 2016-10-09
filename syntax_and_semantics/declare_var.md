# Déclaration de variable non initialisée

Crystal permet de déclarer des variables non initialisées:

```crystal
x = uninitialized Int32
x #=> valeur aléatoire, pourrie, non sûre
```

Ceci est du code [unsafe](unsafe.html) et est quasiment toujours utilisé dans du code bas niveau
pour déclarer des tampons non initialisés de [StaticArray](http://crystal-lang.org/api/StaticArray.html) sans impact sur les performances:

```crystal
buffer = uninitialized UInt8[256]
```

Le tampon est alloué sur la pile, pour éviter les allocations sur le tas.

Le type après le mot clé `uninitialized` suit la [grammaire de type](type_grammar.html).
