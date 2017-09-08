# Le langage de programmation Crystal

Ceci est la documentation du langage de programmation Crystal.

Crystal est un langage de programmation qui a pour buts:

* Avoir une syntaxe similaire à Ruby (mais la compatibilité n'est pas un but),
* Un typage statique, mais sans avoir à spécifier le type des variables ou des arguments de méthode,
* Pouvoir exécuter du code C en écrivant des bindings depuis Crystal,
* D'avoir une évaluation et une génération du code à la compilation, pour éviter le code générique.
* Compilation en du code natif performant.

## Génération

```
$ npm install -g gitbook-cli
$ gitbook build --gitbook=2.3.2
```

Le Html sera généré dans le dossier `_book`.

## Contribuer

Vous pensez être utile? Si vous trouvez des bugs ou des sections
qui ont besoin d'éclaircissements vous êtes bienvenue pour participer
à cette documentation. Vous pouvez soumettre une pull request à ce dépôt:
https://github.com/crystal-lang/crystal-book

Merci beaucoup!

## Géneration et Server Local

```
$ git clone https://github.com/crystal-lang/crystal-book.git
$ cd crystal-book
$ npm install -g gitbook-cli@2.3.0
$ npm install
$ gitbook serve
Live reload server started on port: 35729
Press CTRL+C to quit ...

info: 8 plugins are installed
info: loading plugin "ga"... OK
...
Starting server ...
Serving book on http://localhost:4000
```
Le Html sera généré dans le dossier `_book` (des liens ne marcherons pas si vous ouvrez le fichier localement). L'environnement docker permet d'éviter l'installation de depandance global:

```
$ docker-compose up
...
gitbook_1  | Starting server ...
gitbook_1  | Serving book on http://localhost:4000
gitbook_1  | Restart after change in file node_modules/.bin
...
```

## Ajout de page
Pour ajouter une page, crée un fichier dans la destination désiré. Exemple `overview/hello_world.md`. Ajouté, un lien dans le fichier `SUMMARY.md` pour se la lisibilité de la documentation.