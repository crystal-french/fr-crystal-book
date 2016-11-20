# Depuis les sources

Si vous désirez devenir contributeur alors vous voudrez installer Crystal depuis les sources.
Mais Crystal est écrit en Crystal! Vous avez donc besoin d'utiliser une des méthodes
décrites précédemment pour avoir un compilateur fonctionnel.

Vous aurez aussi besoin de LLVM 3.5 ou 3.6 accessible dans votre $PATH.
Si vous êtes sous Mac et utilisez Homebrew, cela sera automatiquement
configuré si vous installez Crystal en ajoutant le drapeau `--with-llvm`.

Clonez ensuite le dépôt:

```
git clone https://github.com/crystal-lang/crystal.git
```

Vous êtes maintenant fin prêt pour coder.

Pour générer votre propre version du compilateur, exécutez `make`. Le nouveau compilateur sera installé dans `.build/crystal`.

Assurez-vous d'installer [toutes les librairies nécessaires](https://github.com/crystal-lang/crystal/wiki/All-required-libraries).
Vous voudrez peut-être également lire le [guide du contributeur](https://github.com/crystal-lang/crystal/blob/master/CONTRIBUTING.md).

Dans le dépôt vous trouverez également un script `bin/crystal`.
Ce script va exécuter le compilateur installé globalement ou celui que vous venez juste de compiler (s'il est présent).
