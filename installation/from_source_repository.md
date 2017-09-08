# Depuis les sources

Si vous désirez devenir contributeur alors vous voudrez installer Crystal depuis les sources.

1. [Installez la dernière version de Crystal](https://crystal-lang.org/docs/installation). Pour compiler Crystal, vous avez besoin de Crystal :).

2. Vous aurez aussi besoin de LLVM 3.5 ou 3.6 accessible dans votre $PATH.
Si vous êtes sous Mac et utilisez Homebrew, cela sera automatiquement
configuré si vous installez Crystal en ajoutant le drapeau `--with-llvm`.

3. Assurez-vous d'installer [toutes les bibliothèques requises](https://github.com/crystal-lang/crystal/wiki/All-required-libraries). Vous pouvez également lire le [guide de contribution](https://github.com/crystal-lang/crystal/blob/master/CONTRIBUTING.md).

4. Clonez ensuite le dépôt:

```
git clone https://github.com/crystal-lang/crystal.git
```

5. Exécutez `make` pour créer votre propre version du compilateur
6. Exécutez `make spec` pour vous assurer que toutes les spécifications passent, et vous avez tout installé correctement.
7. Utilisez `bin/crystal` pour exécuter vos fichiers en cristal

Si vous souhaitez plus d'informations sur le nouveau `bin/crystal`, consultez la documentation [utiliser le compilateur](https://crystal-lang.org/docs/using_the_compiler/).

Remarque: Le binaire réel est intégré à `.build/crystal`, mais le script enveloppe` bin/crystal` est-ce que vous devez utiliser pour exécuter du cristal.