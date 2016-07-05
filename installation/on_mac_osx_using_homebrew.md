# Sur Mac OSX avec Homebrew

Pour installer facilement Crystal sur Mac vous pouvez utiliser [Homebrew](http://brew.sh/).

```
brew update
brew install crystal-lang
```

Si vous comptez contribuer au projet vous pourrez trouver également utile d'installer LLVM.
Remplacer la dernière commande par:

```
brew install crystal-lang --with-llvm
```

## Dépannage sur OSX 10.11 (El Capitan)

Si vous avez l'erreur suivante:

```
ld: library not found for -levent
```

Vous devez réinstaller les outils en ligne de commande, puis choisir
la tolchain à activer par défaut:

```
$ xcode-select --install
$ xcode-select --switch /Library/Developer/CommandLineTools
```
