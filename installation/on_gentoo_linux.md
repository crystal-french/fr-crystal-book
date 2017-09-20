# Sur Gentoo Linux

Gentoo Linux inclut le compilateur Crystal dans la couche principale.

## Configuration

Vous voudrez peut-être jeter un oeil aux flags de configuration disponibles dans un premier temps:

```
# equery u dev-lang/crystal
[ Légende : U - paramètre du flag final pour l'installation]
[        : I - paquet installé avec le flag                ]
[ Couleurs : set, unset                                    ]
 * Trouvez ces USE flags pour dev-lang/crystal-0.18.7:
 U I
 - - doc      : Ajoute une documentation supplémentaire (API, Javadoc, etc.). Il est recommandé d'activer par paquet au lieu de le faire globalement
 - - examples : Installe des exemples, généralement du code source
 + + xml      : Utilise la librairie dev-libs/libxml2 pour activer le module xml de Crystal
 + - yaml     : Utilise la librairie dev-libs/libyaml pour activer le module yaml de Crystal
```

## Installation

```
su -
emerge -a dev-lang/crystal
```
