# Variables fraîches

Dès que les macros générent du code, elles sont parsées avec un parseur Crystal ordinaire où
les variables locales dans le contexte des invocations de la macro sont considérées comme définies.

Un exemple permettra de mieux le comprendre:

```crystal
macro update_x
  x = 1
end

x = 0
update_x
x #=> 1
```

Cela peut parfois être utile pour éviter du code répétitif en lisant/écrivant délibérément les variables locales,
mais peut aussi réécrire les variables locales par erreur.
Pour éviter cela, des variables fraîches peuvent être déclarées avec `%name`:

```crystal
macro dont_update_x
  %x = 1
  puts %x
end

x = 0
dont_update_x # outputs 1
x #=> 0
```

En utilisant `%x` dans l'exemple précédent, nous déclarons une variable dont le nom est garanti de ne pas entrer
en conflit avec les variables locales de la portée courante.

De plus, des variables fraîches en lien avec d'autres noeuds AST peuvent être déclarées avec `%var{key1, key2, ..., keyN}`. Par exemple:

```crystal
macro fresh_vars_sample(*names)
  # First declare vars
  {% for name, index in names %}
    print "Declaring: ", "%name{index}", '\n'
    %name{index} = {{index}}
  {% end %}

  # Then print them
  {% for name, index in names %}
    print "%name{index}: ", %name{index}, '\n'
  {% end %}
end

fresh_vars_sample a, b, c

# Sample output:
# Declaring: __temp_255
# Declaring: __temp_256
# Declaring: __temp_257
# __temp_255: 0
# __temp_256: 1
# __temp_257: 2
```

Dans l'exemple précédent, trois variables indexées sont déclarées, des valeurs leur sont affectées, puis affichées, dévoilant
leur indice correspondant.
