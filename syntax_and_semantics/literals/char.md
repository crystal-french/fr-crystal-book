# Char

Un [Char](http://crystal-lang.org/api/Char.html) représente un [Unicode](https://fr.wikipedia.org/wiki/Unicode)
[point de code](http://fr.wikipedia.org/wiki/Point_de_code).
Il occupe 32 bits.

Il est créé en encadrant un caractère UTF-8 entre guillemets simples.

```crystal
'a'
'z'
'0'
'_'
'あ'
```

Vous pouvez utiliser un antislash pour protéger certains caractères:

```crystal
'\'' # guillement simple
'\\' # antislash
'\e' # échappement
'\f' # saut de page
'\n' # nouvelle ligne
'\r' # retour chariot
'\t' # tabulation
'\v' # tabulation verticale
```

Vous pouvez utiliser un antislash suivi d'un *u* et quatre caractères hexadécimaux pour représenter un point de code unicode:

```crystal
'\u0041' # == 'A'
```

Vous pouvez utiliser des accolades pour représenter un hexadécimal jusqu'à 6 nombres (0 à 10FFFF):

```crystal
'\u{41}'    # == 'A'
'\u{1F52E}' # == '🔮'
```
