# Regex

Les expressions régulières sont représentées par la classe [Regex](http://crystal-lang.org/api/Regex.html),
qui sont généralement créées avec un litéral:

```crystal
foo_or_bar = /foo|bar/
heeello    = /h(e+)llo/
integer    = /\d+/
```

Un litéral d'expression régulière est délimité par des `/` et utilise la syntaxe [PCRE](http://pcre.org/pcre.txt).

Il peut-être suivi par ces modificateurs:

* i: ignorer la casse (PCRE_CASELESS)
* m: multi-lignes (PCRE_MULTILINE)
* x: élargie (PCRE_EXTENDED)

Par exemple:

```crystal
r = /foo/imx
```

Les slashes doivent être protégés:

```crystal
slash = /\//
```

Une syntaxe alternative est disponible:

```crystal
r = %r(regex avec slash: /)
```
