# Performance

Suivez ces conseils pour tirer le meilleur de vos programmes, aussi bien en termes de vitesse que d'utilisation mémoire.

## Optimisation prématurée

Donald Knuth a dit:

> Nous devrions oublier les petites améliorations, disons 97% du temps:
l'optimisation prématurée est la source de tous les  maux. Pourtant nous ne devrions pas décliner les opportunités de ces 3% critiques.

Néanmoins, si vous écrivez une programme et vous réalisez qu'écrire une version plus rapide sémantiquement équivalente requiert seulement des modifications mineures, vous ne devriez pas rater cette opportunité.

Et assurez-vous de toujours profiler votre programme pour en connaître les goulots d'étranglement.
Pour le profilage, sous Mac OSX vous pouvez utiliser les
[Instruments du Time Profiler](https://developer.apple.com/library/prerelease/content/documentation/DeveloperTools/Conceptual/InstrumentsUserGuide/Instrument-TimeProfiler.html) fournis avec Xcode.
Sous Linux, un programme qui peut profiler des programmes C/C++, comme [gprof](https://sourceware.org/binutils/docs/gprof/), devrait faire l'affaire.

Assurez-vous de toujours profiler les programmes en les compilant ou les exécutant avec le drapeau `--release`, qui active les optimisations.

## Eviter les allocations mémoires

Une des meilleures optimisations possibles que vous pouvez apporter à un programme est d'éviter les allocations mémoires supperflues.
Une allocation mémoire à lieu lorsque vous créez une instance d'une **classe**,
qui se conclut par l'allocation de mémoire sur le tas. Créer une instance d'une **struct** utilise la mémoire de la pile et n'a aucun impact sur les performances.

Si vous ne connaissez pas la différence entre la pile et le tas, assurez-vous de [lire ceci](https://fr.wikipedia.org/wiki/Allocation_de_m%C3%A9moire).

L'allocation sur le tas est lente, et il donne plus de travail au Ramasse-miettes car il aura à libérer cette mémoire plus tard.

Il y a plusieurs moyens d'éviter les allocations sur le tas. La librairie standard est conçue pour vous aider en cela.

### Ne créez pas de chaînes de caractères intermédiaires quand vous écrivez dans une IO

Pour afficher un nombre sur la sortie standard vous écrivez:

```
puts 123
```

Dans beaucoup de langages de programmation ce qui se passe est que `to_s`, ou une méthode similaire pour convertir un objet dans sa représentation sous frome de string, sera invoquée, puis cette string sera affichée sur la sortie standard. Cela fonctionne, mais avec un bémol: une string intermédiaire est créée, sur le tas, seulement pour l'afficher puis s'en débrasser.
Cela fait appel à une allocation sur le tas et rajoute du travail au Ramasse-miettes.

Avec Crystal, `puts` va invoquer `to_s(io)`, sur l'objet, en lui passant l'IO vers laquelle écrire la représentation sous forme de string.

Ainsi, vous ne devriez jamais faire ceci:

```
puts 123.to_s
```

car cela va créer une string intermédiaire. Ajoutez toujours un objet directement à une IO.

Quand vous écrivez des types personnalisés, assurez-vous de redéfinir `to_s(io)`, et non `to_s`,
et évitez de créer des strings intermédiaires dans cette méthode. Par exemple:

```crystal
class MyClass
  # Bon
  def to_s(io)
    # ajoute "1, 2" à IO sans créer des strings intermédiaires
    x = 1
    y = 2
    io << x << ", " << y
  end

  # Mauvais
  def to_s(io)
    x = 1
    y = 2
    # utiliser une interpolation de string crée une string intermédiaire
    # qui devrait être à éviter
    io << "#{x}, #{y}"
  end
end
```

Cette philosophie d'ajouter à une IO au lieu de retourner une string intermédiaire est présente dans d'autres APIs, comme dans les APIs JSON et YAML, où l'on doit définir des méthodes `to_json(io)` et `to_yaml(io)` pour écrire ces données directement dans une IO. Et vous devriez utiliser cette stratégie dans les définitions de vos APIS également.

Comparons le temps d'exécution:

```crystal
# io_benchmark.cr
require "benchmark"

io = MemoryIO.new

Benchmark.ips do |x|
  x.report("without to_s") do
    io << 123
    io.clear
  end

  x.report("with to_s") do
    io << 123.to_s
    io.clear
  end
end
```

Sortie:

```
$ crystal io_benchmark.cr --release
without to_s  69.71M (± 6.14%)       fastest
   with to_s  14.68M (± 2.38%)  4.75× slower
```

Et gardez à l'esprit que nous n'avons pas simplement amélioré le temps d'exécution: l'utilisation mémoire a aussi été diminuée.

### Eviter de créer des objets temporaires de manière répétée

Considérons ce programme:

```crystal
lines_with_language_reference = 0
while line = gets
  if ["crystal", "ruby", "java"].any? { |string| line.includes?(string) }
    lines_with_language_reference += 1
  end
end
puts "Lines that mention crystal, ruby or java: #{lines_with_language_reference}"
```

Le programme précédent est fonctionnel mais à un gros probléme de performance:
à chaque itération un nouveau tableau est créé pour `["crystal", "ruby", "java"]`.
Rappelez-vous, un litéral de tableau est juste du sucre syntaxique pour créer une instance d'un tableau et lui ajouter des éléments, et ceci sera répété à chaque iération.

Il y a deux façons de remédier à cela:

1. Utiliser un tuple. Si vous utilisez `{"crystal", "ruby", "java"}` dans le programme précédent il fonctionnera de la même manière,
mais étant donné qu'un tuple ne met pas en jeu de la mémoire sur le tas il sera plus rapide, consommera moins de mémoire et donnera plus de chances au compilateur d'optimiser le programme.

  ```crystal
  lines_with_language_reference = 0
  while line = gets
    if {"crystal", "ruby", "java"}.any? { |string| line.includes?(string) }
      lines_with_language_reference += 1
    end
  end
  puts "Lines that mention crystal, ruby or java: #{lines_with_language_reference}"
  ```

2. Passer le tableau en une constante.

  ```crystal
  LANGS = ["crystal", "ruby", "java"]

  lines_with_language_reference = 0
  while line = gets
    if LANGS.any? { |string| line.includes?(string) }
      lines_with_language_reference += 1
    end
  end
  puts "Lines that mention crystal, ruby or java: #{lines_with_language_reference}"
  ```

L'utilisation de tuple est à privilégier.

Des litéraux de tableau explicites dans les boucles est un moyen de créer des objets temporaires, mais ils peuvent aussi être créés via des appels de méthode.
Par exemple `Hash#keys` retournera un nouveau tableau avec les clés à chaque fois qu'il est invoqué.
Au lieu de cela, vous pouvez utiliser `Hash#each_key`, `Hash#has_key?` et d'autres méthodes.

### Utilisez des structs autant que possible

Si vous déclarez votre type en tant que **struct** au lieu d'une **class**,
la création d'une instance utilisera la pile, ce qui est moins gourmand que le tas et ne rajoute pas du travail au Ramasse-miettes.

Vous ne devriez néanmoins pas toujours utiliser de struct. Les structs sont passées par valeur, ainsi si vous en passez une à une méthode et la méthode la modifie,
l'appelant ne verra pas ces modifications, ce qui peut être source de bugs.
La meilleure des choses à faire est d'utiliser uniquement des structs pour des objets immuables, surtout si elles sont petites.

Par exemple:

```crystal
# class_vs_struct.cr
require "benchmark"

class PointClass
  getter x
  getter y

  def initialize(@x : Int32, @y : Int32)
  end
end

struct PointStruct
  getter x
  getter y

  def initialize(@x : Int32, @y : Int32)
  end
end

Benchmark.ips do |x|
  x.report("class") { PointClass.new(1, 2) }
  x.report("struct") { PointStruct.new(1, 2) }
end
```

Sortie:

```
$ crystal class_vs_struct.cr --release
 class  28.17M (± 2.86%) 15.29× slower
struct 430.82M (± 6.58%)       fastest
```

## Itération sur les strings

Les strings dans Crystal contiennent toujours des bytes encodés en UTF-8. UTF-8 est un encodage à longueur variable:
un caractère peut être représenté sur plusieurs bytes, alors que les caractères en ASCII sont toujours représentés sur un seul byte.
A cause de cela, indexer une string avec `String#[]` n'est pas une opération `O(1)`, car à chaque fois les bytes ont besoin d'être décodés pour trouver le caractère à une position donnée.
Il y a une optimisation faite par les Strings Crystal: si Crystal sait que tous les caractères de la string sont en ASCII, alors `String#[]` peut être implémentée en `O(1)`. Néanmoins, ce n'est pas toujours le cas.

Pour cette raison, itérer sur une String de cette manière n'est pas optimale, et en fait c'est plus de l'ordre de `O(n^2)`:


```crystal
string = ...
while i < string.size
  char = string[i]
  # ...
end
```

Il y a un second problème avec ce qui précéde: calculer la taille (`size`) d'une String est aussi lent, parce-que cela ne tient pas juste au nombre de bytes dans la string (la `bytesize`).
Néanmoins, dès qu'une String calcule sa taille elle la met en cache. C'est toujours lent à cause de `String#[]`.

La méthode pour faire cela est soit d'utiliser une des méthodes d'itération (`each_char`, `each_byte`, `each_codepoint`),
ou utiliser la struct plus bas niveau `Char::Reader`. Par exemple, en utilisant `each_char`:

```crystal
string = ...
string.each_char do |char|
  # ...
end
```
