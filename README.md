# Le langage de programmation Crystal

Ceci est la documentation du langage de programmation Crystal.

Crystal est un langage de programmation qui a pour buts:

* Avoir une syntaxe similaire à Ruby (mais la compatibilité n'est pas un but),
* Un typage statique, mais sans avoir à spécifier le type des variables ou des arguments de méthode,
* Pouvoir exécuter du code C en écrivant des bindings depuis Crystal,
* Have compile-time evaluation and generation of code, to avoid boilerplate code.
* Compilation en du code natif performant.

## Génération

```
$ npm install -g gitbook-cli
$ gitbook build --gitbook=2.3.2
```

Le Html sera généré dans le dossier `_book`.
