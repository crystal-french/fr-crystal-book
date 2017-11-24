# lib

Une déclaration `lib` regroupe des fonctions et types C appartenant à une librairie.

```crystal
@[Link("pcre")]
lib LibPCRE
end
```

Bien que ce ne soit pas forcé par le compilateur, le nom d'une `lib` démarre généralement par `Lib`.

Les attributs sont utilisés pour passer des drapeaux au lieur (linker) pour trouver des librairies externes:

* `@[Link("pcre")]` va passer `-lpcre` au linker, mais le compilateur commencera par utiliser [pkg-config](http://fr.wikipedia.org/wiki/Pkg-config).
* `@[Link(ldflags: "...")]` va passer les drapeaux donnés directement au linker, sans modification.
Par exemple: `@[Link(ldflags: "-lpcre")]`. Une technique commune est d'utiliser des quotes inverses pour exécuter des commandes: ``@[Link(ldflags: "`pkg-config libpcre --libs`")]``.
* `@[Link(framework: "Cocoa")]` va passer `-framework Cocoa` au linker (seulement utile avec Mac OS X).

Les attributs peuvent être omis si la librairie est implicitement liée, comme c'est le cas de la libc.
