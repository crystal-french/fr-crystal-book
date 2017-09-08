# Sur Bash sur Ubuntu sur Windows

Crystal ne supporte pas _encore_ Windows, mais si vous utilisez Windows 10 vous pouvez (expérimentalement) essayer Crystal en utilisant [Bash on Ubuntu on Windows](https://msdn.microsoft.com/en-us/commandline/wsl/about), une version expérimentale de l'environnement Bash fonctionnant sur Windows. Les instructions d'installation sont les mêmes que pour [Debian/Ubuntu](on_debian_and_ubuntu.md), mais il y a quelques parties rugueuses à prendre en compte.

N'oubliez pas - **c'est vraiment expérimental**.

## Dossier d'installation

D'abord, vous devez ajouter le référentiel à votre configuration APT. Pour une configuration simple, utilisez simplement votre ligne de commande:

```
curl -sSL https://dist.crystal-lang.org/apt/setup.sh | sudo bash
```

Cela ajoutera la clé de signature et la configuration du référentiel. Si vous préférez le faire manuellement, exécutez les commandes suivantes:

```
sudo apt-key adv --keyserver keys.gnupg.net --recv-keys 09617FD37CC06B54
echo "deb https://dist.crystal-lang.org/apt crystal main" | sudo tee /etc/apt/sources.list.d/crystal.list
sudo apt-get update
```

## Dépendances
Crystal a besoin d'un compilateur C (`cc`) et d'un linker (` ld`) pour pouvoir compiler des programmes Crystal, donc vous devez les installer:

```
sudo apt-get install clang binutils
```

## Installation
Une fois que le référentiel est configuré et que vous avez les dépendances, vous êtes prêt à installer Crystal:

```
sudo apt-get install crystal
```

## Mise à jour

A la sortie d'une nouvelle version de Crystal vous pouvez mettre à jour votre système en exécutant:

```
sudo apt-get update
sudo apt-get install crystal
```
