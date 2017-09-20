# Sur Debian et Ubuntu

Sur les distributions Debian et dérivées, vous pouvez utiliser le dépôt officiel Crystal.

## Configurer le dépôt

Vous devez commencer par ajouter le dépôt à votre configuration APT.
Exécutez simplement la ligne de commande:

```
curl https://dist.crystal-lang.org/apt/setup.sh | sudo bash
```

Cela a pour effet d'ajouter la clé et la configuration du dépôt.
Si vous préférez le faire manuellement, exécutez les commandes suivantes en tant que *root*:

```
apt-key adv --keyserver keys.gnupg.net --recv-keys 09617FD37CC06B54
echo "deb https://dist.crystal-lang.org/apt crystal main" > /etc/apt/sources.list.d/crystal.list
apt-get update
```

## Installation
Une fois le dépôt configuré, vous êtes prêt pour installer Crystal:

```
sudo apt-get install crystal
```

Parfois [vous aurez besoin](https://github.com/crystal-lang/crystal/issues/4342) d'installer le paquet `build-essential` afin de lancer/contruire les programmes Crystal. Vous pouvez installer le paquet à l'aide des commandes suivantes:

```
sudo apt-get install build-essential
```


## Mise à jour

A la sortie d'une nouvelle version de Crystal vous pouvez mettre à jour votre système en exécutant:

```
sudo apt-get update
sudo apt-get install crystal
```
