# Variables locales

Les variables locales commencent avec une lettre minuscule.
Elles sont déclarées lors de la première affectation.

```crystal
name = "Crystal"
age = 1
```

Leur type est déduit de leur usage, pas seulement de leur initialisateur.
En règle générale, ce sont juste des conteneurs de valeur associés au type
attendu par le développeur par rapport à leur place et usage dans le programme.

Par exemple, re-affecter une variable avec une expression différente lui donne le type de cette expression:

```crystal
flower = "Tulip"
# A ce stade 'flower' est une String

flower = 1
# A ce stade 'flower' est un Int32
```

Les soulignés sont permis au début du nom d'une variable,
mais ces noms sont réservés au compilateur, leur utilisation
n'est donc pas recommendée (et cela rend également le code difficile à lire).
