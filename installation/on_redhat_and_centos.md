# Sur RedHat  CentOS

Sur les distributions RedHat et dérivées, vous pouvez utiliser le dépôt officiel Crystal.

## Configurer le dépôt

Vous devez commencer par ajouter le dépôt à votre configuration YUM.
Exécutez simplement la ligne de commande:

```
  curl https://dist.crystal-lang.org/rpm/setup.sh | sudo bash
```

Cela a pour effet d'ajouter la clé et la configuration du dépôt.
Si vous préférez le faire manuellement, exécutez les commandes suivantes en tant que *root*:

```
rpm --import https://dist.crystal-lang.org/rpm/RPM-GPG-KEY

cat > /etc/yum.repos.d/crystal.repo <<END
[crystal]
name = Crystal
baseurl = https://dist.crystal-lang.org/rpm/
END
```

## Installation

Une fois le dépôt configuré, vous êtes prêt pour installer Crystal:

```
sudo yum install crystal
```

## Mise à jour

A la sortie d'une nouvelle version de Crystal vous pouvez mettre à jour votre système
en exécutant:

```
sudo yum update crystal
```
