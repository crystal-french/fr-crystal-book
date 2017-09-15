# Affectation

Une affectation est effectuée avec le caractère égal (`=`).

```crystal
# Affectation d'un variable locale
local = 1

# Affectation d'une variable d'instance
@instance = 2

# Affectation d'une variable de classe
@@class = 3


Chacun de ces différents types de variables sera expliqué plus tard.

Quelques sucres syntaxiques qui contiennent le caractère `=` sont disponibles:

```crystal
local += 1  # équivalent à: local = local + 1

# L'exemple précédent est valide avec les opérateurs suivants:
# +, -, *, /, %, |, &, ^, **, <<, >>

local ||= 1 # équivalent à: local || (local = 1)
local &&= 1 # équivalent à: local && (local = 1)
```

Une invocation de méthode qui finit par `=` a du sucre syntaxique:

```crystal
# Un modificateur
person.name=("John")

# L'exemple précédent peut être écrit:
person.name = "John"

# Une assignation indexée
objects.[]=(2, 3)

# L'exemple précédent peut être écrit:
objects[2] = 3

# Pas du domaine de l'affectation, mais encore du sucre syntaxique:
objects.[](2, 3)

# L'exemple précédent peut être écrit:
objects[2, 3]
```

Le sucre syntaxique de l'opérateur `=` est aussi disponible pour les modificateurs et les indexeurs.
Notez que `||` et `&&` utilisent la méthode `[]?` pour vérifier la présence d'une clé.

```crystal
person.age += 1        # équivalent à: person.age = person.age + 1

person.name ||= "John" # équivalent à: person.name || (person.name = "John")
person.name &&= "John" # équivalent à: person.name && (person.name = "John")

objects[1] += 2        # équivalent à: objects[1] = objects[1] + 2

objects[1] ||= 2       # équivalent à: objects[1]? || (objects[1] = 2)
objects[1] &&= 2       # équivalent à: objects[1]? && (objects[1] = 2)
```
