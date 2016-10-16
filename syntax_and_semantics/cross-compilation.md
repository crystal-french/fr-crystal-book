# Compilation croisée

Crystal supporte une forme basique de [compilation croisée](http://en.wikipedia.org/wiki/Cross_compiler).

Afin d'y arriver, le compilateur fournit deux drapeaux:

* `--cross-compile`: Active le mode compilation croisée
* `--target`: la [cible triple LLVM](http://llvm.org/docs/LangRef.html#target-triple) à utiliser
   et définit [drapeaux de compilation](compile_time_flags.html) avec

Pour avoir les drapeaux `--target` vous pouvez exécuter `llvm-config --host-target` avec LLVM 3.5 installé.
Par exemple sous Linux vous pourrez obtenir "x86_64-unknown-linux-gnu".

Si vous avez besoin de définir tout drapeau de compilation non défini implicitement via `--target`,
vous pouvez utiliser le drapeau de ligne de commande `-D`.

À l'aide des deux, nous pouvons compiler un programme depuis Mac qui fonctionnera sous Linux comme suit:

```bash
crystal build your_program.cr --cross-compile --target "x86_64-unknown-linux-gnu"
```

Cela généra un fichier `.o` ([Fichier objet](http://en.wikipedia.org/wiki/Object_file)) et va afficher une ligne
avec une commande à exécuter sur le système vers lequel nous faire la compilation croisée. Par exemple:

```bash
cc your_program.o -o your_program -lpcre -lrt -lm -lgc -lunwind
```

Vous pouvez copier ce fichier `.o` vers ce système et exécuter ces commandes.
Une fois fait l'exécutable sera disponible sur le système cible.

Cette procédure est généralement faite avec le compilateur lui-même pour le porter vers de nouvelles plate-formes sur lesquelles
il n'est pas disponible. Car dans le but de compiler un compilateur Crystal nous avons besoin d'un ancien compilateur Crystal,
les deux seules manières de générer un compilateur pour un système sur lequel il n'y a pas encore de compilateur sont:
* Récupérer la dernière version du compilateur écrite en Ruby,
et depuis ce compilateur compiler les versions suivantes jusqu'à la version courante.
* Créer un fichier `.o` sur le système cible et depuis celui-ci créer un compilateur.

La première méthode est longue et fastidieuse, alors que la deuxième est beaucoup plus simple.

La compilation croisée peut être utilisée pour d'autres exécutables,
mais son utilisation cible principale est le compilateur.
Si Crystal n'est pas dispo sur certains systèmes vous pouvez tenter la compilation croisée pour ceux-ci.
