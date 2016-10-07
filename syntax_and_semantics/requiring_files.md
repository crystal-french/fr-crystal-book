# Utiliser des fichiers

Ecrire un programme dans un seul fichier convient pour des petits bouts code et benchmarks.
De plus gros programmes sont plus simples à maintenir et à comprendre quand ils sont découper en différents fichiers.

Pour que le compilateur traite d'autres fichiers on utilise `require "..."`.
Il prend un seul argument, un litéral chaîne de caractères, et peut prendre plusieurs formes d'utilisation.

Une fois qu'un fichier est inclus, le compilateur se souvient de son chemin absolu et des `require`s ultérieurs de ce même fichier seront ignorés.

## require "fichier"

Cela recherche "fichier" dans le chemin par défaut.

Le chemin par défaut est l'emplacement de la librairie standard fournie avec le compilateur,
et le dossier "libs" relatif au dossier de travail courant (donné par la commande `pwd` dans un shell unix).
Ceux sont les seuls emplacements recherchés.

Cette recherche se passe comme suit:

* Si un fichier "fichier.cr" est trouvé dans le chemin de recherche, il est utilisé,
* Si un dossier nommé "fichier" est trouvé et contient un fichier nommé "fichier.cr" à sa racine, il est utilisé,
* Sinon une erreur à la compilation est renvoyée.

La seconde règle est très pratique de par l'arborescence typique d'un projet:

```
- project
  - libs
    - foo
      foo.cr
    - bar
      bar.cr
  - src
    - project.cr
  - spec
    - project_spec.cr
```

## require "./fichier"

Cela recherche "fichier" relativement au fichier contenant l'expression `require`.

Cette recherche se passe comme suit:

* Si un fichier "fichier.cr" est trouvé dans le chemin de recherche, il est utilisé,
* Si un dossier nommé "fichier" est trouvé et contient un fichier nommé "fichier.cr" à sa racine, il est utilisé,
* Sinon une erreur à la compilation est renvoyée.

Cette recherche relative est souvent utilisé dans un projet pour faire référence à d'autres fichiers du projet.
C'est aussi utilisé pour faire référence à du code depuis les spécifications:

```crystal
# in spec/project_spec.cr
require "../src/project"
```

## Autres formes

Dans chaque cas vous pouvez utiliser des noms imbriqués et ils seront recherchés dans les sous-dossiers correspondants:

* `require "foo/bar/baz"` va rechercher "foo/bar/baz.cr" ou "foo/bar/baz/baz.cr" dans le chemin par défaut.
* `require "./foo/bar/baz"` va rechercher "foo/bar/baz.cr" ou "foo/bar/baz/baz.cr" dans le chemin relatif au fichier courant.

Vous pouvez aussi utiliser "../" pour accéder aux dossiers parents du fichier courant,
ainsi `require "../../foo/bar"` march etout aussi bien.

Dans tous ces cas vous pouvez utiliser les suffixes `*` et `**`:

* `require "foo/*"` va utiliser tous les fichiers ".cr" du le dossier "foo", mais pas les sous-dossiers de "foo".
* `require "foo/**"` va utiliser tous les fichiers ".cr" du le dossier "foo", et les sous-fichiers de "foo", récursivement.
