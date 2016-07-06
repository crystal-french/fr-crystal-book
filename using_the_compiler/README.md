# Utilisater le compilateur

Une fois que vous avez procédé à l'[installation](../installation/README.md)
du compilateur vous aurez un binaire `crystal` à disposition.

Dans les sections qui suivent un signe (`$`) indique le prompt de la ligne de commande.

## Compilation et exécution en même temps

Pour compiler et exécuter un programme d'un coup vous pouvez invoquer `crystal` avec un unique fichier:

```
$ crystal some_program.cr
```

Les fichiers Crystal ont `.cr` comme extension.

Vous pouvez également utiliser la commande `run`:

```
$ crystal run some_program.cr
```

## Création d'un exécutable

Pour créer un exécutable, utilisez la commande `build`:

```
$ crystal build some_program.cr
```

Cela a pour effet de créer un fichier `some_program` que vous pouvez exécuter:

```
$ ./some_program
```

**Note:** Par défaut les exécutables générés **ne sont pas complétement optimisés**.
Pour activer les optimisations, utilisez le drapeau `--release`:

```
$ crystal build some_program.cr --release
```

Assurez-vous de toujours utiliser `--release` pour des exécutables à distribuer ou lorsque vous faites des tests de performance.

Les performances sans toutes les optimisations sont plus qu'acceptables avec un temps de compilation rapide,
vous pouvez donc exécuter la commande `crystal` comme si c'était un interpréteur.

## Création d'un projet ou d'une librairie

Utilisez la commande `init` pour créer un projet Crystal avec la structure de dossier standard.

```
$ crystal init lib MyCoolLib
      create  MyCoolLib/.gitignore
      create  MyCoolLib/LICENSE
      create  MyCoolLib/README.md
      create  MyCoolLib/.travis.yml
      create  MyCoolLib/shard.yml
      create  MyCoolLib/src/MyCoolLib.cr
      create  MyCoolLib/src/MyCoolLib/version.cr
      create  MyCoolLib/spec/spec_helper.cr
      create  MyCoolLib/spec/MyCoolLib_spec.cr
Initialized empty Git repository in ~/MyCoolLib/.git/
```

## Autres commandes et options

Pour voir une liste complète des commandes, invoquez `crystal` sans argument

```
$ crystal
Usage: crystal [command] [switches] [program file] [--] [arguments]

Command:
    init                     generate a new project
    build                    build an executable
    deps                     install project dependencies
    docs                     generate documentation
    env                      print Crystal environment information
    eval                     eval code from args or standard input
    play                     starts crystal playground server
    run (default)            compile and run program
    spec                     compile and run specs (in spec directory)
    tool                     run a tool
    help, --help, -h         show this help
    version, --version, -v   show version
```

Pour voir toutes les options disponibles pour une commande en particulier,
utilisez `--help` après la commande:

```
$ crystal build --help
Usage: crystal build [options] [programfile] [--] [arguments]

Options:
    --cross-compile                  cross-compile
    -d, --debug                      Add symbolic debug info
    -D FLAG, --define FLAG           Define a compile-time flag
    --emit [asm|llvm-bc|llvm-ir|obj] Comma separated list of types of output for the compiler to emit
    -f text|json, --format text|json Output format text (default) or json
    -h, --help                       Show this message
    --ll                             Dump ll to .crystal directory
    --link-flags FLAGS               Additional flags to pass to the linker
    --mcpu CPU                       Target specific cpu type
    --no-color                       Disable colored output
    --no-codegen                     Don't do code generation
    -o                               Output filename
    --prelude                        Use given file as prelude
    --release                        Compile in release mode
    -s, --stats                      Enable statistics output
    --single-module                  Generate a single LLVM module
    --threads                        Maximum number of threads to use
    --target TRIPLE                  Target triple
    --verbose                        Display executed commands
```
