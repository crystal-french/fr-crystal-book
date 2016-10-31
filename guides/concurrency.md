# Concurrence

## Concurrence vs. Parallélisme

Les notions de "concurrence" et "parallélisme" sont parfois confondues, mais sont différentes.

Un système concurrent est un système en charge de plusieurs tâches, mais qu'il n'exécute pas forcément en même temps.
Imaginez que vous êtes en train de faire à manger dans la cuisine: vous épluchez un oignon, vous le mettez à frire, et alors qu'il est en train de frire
vous épluchez une tomate, mais vous ne faites pas toutes ces choses en même temps: vous partagez votre temps entre ces différentes tâches.
Le parallélisme reviendrait à remuer l'oignon en train de cuire d'une main tout en épluchant une tomate de l'autre main.

Alors que vous lisez ces lignes, Crystal supporte la concurrence mais pas le parallélisme:
plusieurs tâches peuvent être exécutées, et un peu de temps sera passé sur chacune d'elle, mais deux portions de code ne seront jamais exécutées exactement en même temps.

Un programme Crystal s'exécute dans un seul thread du système d'exploitation, excepté le Ramasse-miettes (Garbage Collector ou GC),
qui implémente un algorithme concurrent "marquant-et-nettoyant" (actuellement [Boehm GC](http://www.hboehm.info/gc/)).

### Fibers

Pour permettre la concurrence, Crystal a les fibers. Une fiber est d'une certaine manière similaire à un thread de système d'exploitation
à la différence qu'elle est beaucoup plus légére et son exécution est gérée en interne par le processus.
Ainsi, un programme va lancer plusieurs fibers et Crystal va s'assurer de les exécuter au bon moment.

### Boucle événementielle

Pour tout ce qui est lié aux I/O il y a une boucle événementielle. Certaines opérations prenant du temps lui sont déléguées,
et pendant que la boucle événementielle attend après la fin de cette opération, le programme peut continuer à exécuter d'autres fibers.
Un exemple simple de cela est lors de l'attente de la réception de données depuis une socket.

### Canaux

Crystal a des canaux (Channels) inspirés de [CSP](https://fr.wikipedia.org/wiki/Communicating_sequential_processes).
Ils permettent de communiquer des données entre des fibers sans partage de mémoire et sans se soucier des verrous, sémaphores et autres structures spéciales.

## Exécution d'un programme

Quand un programme débute, il démarre une fiber principale qui exécutera votre code principal. De là, d'autres fibers peuvent être démarrées.
Les composants d'un programme sont:

* L'ordonnanceur d'exécution (The Runtime Scheduler), en charge d'exécuter toutes les fibers au bon moment.
* La boucle (The Event Loop), qui est simplement une autre fiber, chargée des tâches asynchrones, comme par exemple les fichiers, sockets, tubes, signaux et timers (comme lors d'un `sleep`).
* Les Channels, pour communiquer des données entre fibers. Le Runtime Scheduler va coordonner fibers et channels pour leur communication.
* Garbage Collector: pour nettoyer la mémoire "plus utilisée".

### Une Fiber

Une fiber est une unité d'exécution qui est plus légère qu'un thread. C'est un petit objet qui a sa propre
[pile d'exécution](https://fr.wikipedia.org/wiki/Pile_d%27ex%C3%A9cution) de 8MB, ce qui est généralement affecté à un thread de système d'exploitation.

Les fibers, à l'inverse des threads, sont collaboratives. Les threads sont pre-emptifs:
Le système d'exploitation peut interrompre un thread à tout moment et démarrer l'exécution d'un autre.
Une fiber doit explicitement dire au Runtime Scheduler de passer à une autre fiber. Par exemple s'il faut attendre après une I/O,
une fiber va dire à l'ordonnanceur  "Ecoute, je dois attendre après cette I/O qu'elle soit disponible, continue d'exécuter d'autres fibers et reviens vers moi
quand cette I/O est prête".

L'avantage d'être collaboratif est que beaucoup de la surcharge liée au changement de contexte (changement entre les threads) n'existe plus.

Une Fiber est beaucoup plus légère qu'un thread: même s'il lui est affecté 8MB, elle débute avec une petite pile de 4KB.

Sur une machine 64-bits cela nous laisse démarrer des millions et millions de fibers.
Dans une machine 32-bits on peut seulement lancer 512 fibers, ce qui est peu.
Mais étant donné que les machines 32-bits tendent à disparaître de plus en plus, on parie sur l'avenir, et pour l'instant nous nous focalisons sur les machines 64-bits.

### The Runtime Scheduler

Le scheduler a une queue composée de :
* Des fibers prêtes à être exécutées: par exemple quand vous lancez une fiber, elle est prête à être exécutée.
* La boucle événementielle: qui est une autre fiber. Quand il n'y a aucune autre fiber à exécuter, la boucle évenementielle vérifie s'il y une opération asynchrone à exécuter,
  puis exécute la fiber en attente de cette opération. La boucle événementielle est actuellement implémentée avec `libevent`,
  qui est une abstraction d'autres mécanismes événementiels comme `epoll` ou `kqueue`.
* Des fibers qui ont délibérément demandé à attendre: obtenu avec `Fiber.yield`, qui signifie
  "je peux continuer l'exécution, mais je vais te donner du temps pour exécuter d'autres fibers si tu le souhaites".

### Communication de données

Parce-que pour le moment il n'y a qu'un seul thread exécutant votre code, accéder et modifier une variable de classe depuis différentes fibers se déroulera sans problème.
Néanmoins, dès que plusieurs threads (parallélisme) seront supportés par le langage, cela ne fonctionnera plus.
C'est pourquoi le mécanisme recommandé pour communiquer des données est d'utiliser les canaux pour passer les messages entre threads.
En interne, un canal implémente tous les mécanismes de verrou pour éviter les courses aux données (data races), mais de l'extérieur vous les utilisez
comme primitives de communication, afin que vous (l'utilisateur) n'ayez à utiliser de verrous.

## Code exemple

### Lancer une fiber

Pour lancer une fiber vous utilisez `spawn` avec un bloc:

```crystal
spawn do
  # ...
  socket.gets
  # ...
end

spawn do
  # ...
  sleep 5.seconds
  #  ...
end
```

Ici nous avons deux fibers: une qui lit depuis une socket et l'autre qui fait un `sleep`.
Quand la première fiber atteint la ligne `socket.gets`, cette fiber est mise en suspens,
il est signifié à la boucle événementielle de continuer à exécuter cette fiber quand il y aura des données dans la socket, et le programme continue avec la deuxième fiber.
Cette fiber veut dormir pour 5 secondes, alors il est demandé à la boucle événementielle de continuer avec cette fiber pour 5 secondes.
S'il n'y a aucune autre fiber à exécuter, la boucle événementielle va attendre qu'un de ces événements se produise, sans consommer de temps CPU.

La raison pour laquelle `socket.gets` et `sleep` se comportent comme cela est parce-que leur implémentation communique directement avec le Runtime Scheduler
et la boucle événementielle, il n'y a rien de magique à cela. En général, la librairie standard prend déjà soin de faire tout ça pour vous éviter d'avoir à le faire vous-même.

Remarquez, néanmoins, que les fibers ne sont pas exécutées immédiatement. Par exemple:

```crystal
spawn do
  loop do
    puts "Hello!"
  end
end
```

Exécuter le code précédent va produire un affichage et sortir immédiatement.

La raison à cela est qu'une fiber n'est pas exécutée dès qu'elle est lancée.
Ainsi, la fiber principale, celle qui lance la fiber précédente, finit son exécution et le programme finit.

Un moyen de résoudre cela est d'utiliser un `sleep`:

```crystal
spawn do
  loop do
    puts "Hello!"
  end
end

sleep 1.second
```

Ce programme va maintenant afficher "Hello!" durant une seconde puis sortir.
C'est parce-que l'appel à `sleep` va planifier l'exécution de la fiber principale dans une seconde, puis exécuter l'autre fiber "prête à être exécutée",
qui dans ce cas est la fiber précédente.

Une autre manière de faire:

```crystal
spawn do
  loop do
    puts "Hello!"
  end
end

Fiber.yield
```

Cetet fois-ci `Fiber.yield` va dire à l'ordonnanceur d'exécuter l'autre fiber.
Cela va afficher "Hello!" jusqu'à ce que la sortie standard bloque (l'appel système va nous dire que nous devons attendre avant que la sortie soit prête),
puis l'exécution continue avec la fiber principale et le programme sort. Ici la sortie standard *peut* ne jamais bloquer et le programme va s'exécuter indéfiniment.

Si nous voulons exécuter indéfiniment la fiber lancée, nous pouvons utiliser `sleep` sans argument:

```crystal
spawn do
  loop do
    puts "Hello!"
  end
end

sleep
```

Bien sûr le programme précédent peut être écrit sans `spawn` du tout, seulement avec une boucle.
`sleep` est plus utile lors du lancement de plus d'une fiber.

### Lancer un appel

Vous pouvez aussi lancer un appel en passant un appel de méthode au lieu d'un bloc.
Pour comprendre pourquoi cela serait utile, voyons un exemple:

```crystal
i = 0
while i < 10
  spawn do
    puts(i)
  end
  i += 1
end

Fiber.yield
```

Le programme précédent affiche "10" dix fois.
Le problème est qu'il n'y a qu'une variable `i` à laquelle toutes les fibers se référent,
et quand `Fiber.yield` est exécutée sa valeur est 10.

Pour résoudre cela, nous pouvons faire:

```crystal
i = 0
while i < 10
  proc = ->(x : Int32) do
    spawn do
      puts(x)
    end
  end
  proc.call(i)
  i += 1
end

Fiber.yield
```

Cela fonctionne tel quel car nous créons un [Proc](http://crystal-lang.org/api/Proc.html) et nous l'invoquons en passant `i`,
alors la valeur est copiée et la fiber lancée en reçoit une copie.

Pour éviter tout ce code, la librairie standard fournit une macro `spawn` qui accepte une expression d'appel
et en gros la réécrit pour faire l'équivalent de ce qui précéde. En l'utilisant, on obtient:

```crystal
i = 0
while i < 10
  spawn puts(i)
  i += 1
end

Fiber.yield
```

C'est surtout utile avec des variables locales qui changent durant des itérations.
Cela n'arrive pas avec des arguments de bloc. Par exemple, ce qui suit a le fonctionnement attendu:

```crystal
10.times do |i|
  spawn do
    puts i
  end
end

Fiber.yield
```

### Lancer une fiber et attendre la fin de son exécution

On peut utiliser un channel pour cela:

```crystal
channel = Channel(Nil).new

spawn do
  puts "Avant envoi"
  channel.send(nil)
  puts "Après envoi"
end

puts "Avant réception"
channel.receive
puts "Après réception"
```

Ce qui affiche:

```text
Avant réception
Avant envoi
Après réception
```

Tout d'abord, le programme lance une fiber mais ne l'exécute pas encore. Quand nous invoquons `channel.receive`,
la fiber principale bloque et l'exécution continue avec la fiber lancée. Ensuite `channel.send(nil)` est invoquée,
et alors l'exécution continue à `channel.receive`, qui était en attente d'une valeur.
Puis la fiber pincipale continue et s'achève, le programme sort donc sans donner à l'autre fiber de chance d'afficher "Après envoi".

Dans l'exemple précédent nous avons utilisé `nil` simplement pour communiquer que la fiber s'est terminée.
Nous pouvons aussi utiliser les canaux pour communiquer des valeurs entre fibers:

```crystal
channel = Channel(Int32).new

spawn do
  puts "Avant le premier envoi"
  channel.send(1)
  puts "Avant le second envoi"
  channel.send(2)
end

puts "Avant la première réception"
value = channel.receive
puts value # => 1

puts "Avant la deuxième réception"
value = channel.receive
puts value # => 2
```

Affichage:

```text
Avant la première réception
Avant le premier envoi
1
Avant la deuxième réception
Avant le second envoi
2
```

Notez que lorsqu'un programme exécute un `receive`, cette fiber bloque et l'exécution se poursuit avec l'autre fiber.
Quand `send` est exécutée, l'exécution continue avec la fiber qui était en attente sur ce canal.

Ici nous envoyons des valeurs litérales, mais la fiber lancée peut calculer cette valeur, par exemple,
en lisant un fichier, ou depuis une socket. Quand cette fiber devra attendre après une I/O,
d'autres fibres pourront continuer à exécuter du code jusqu'à ce que cette I/O soit prête,
puis finalement quand cette valeur est prête et envoyée via le canal, la fiber principale la recevra. Par exemple:

```crystal
require "socket"

channel = Channel(String).new

spawn do
  server = TCPServer.new("0.0.0.0", 8080)
  socket = server.accept
  while line = socket.gets
    channel.send(line)
  end
end

spawn do
  while line = gets
    channel.send(line)
  end
end

3.times do
  puts channel.receive
end
```

Le programme précédent lance deux fibers. La première crée un TCPServer, accepte une connexion et lit depuis des lignes,
et les envoie au canal. Il y a une seconde fiber qui lit des lignes depuis l'entrée standard. La fiber principale
lit les 3 messages qui sont envoyés sur le canal, soit depuis la socket ou stdin, puis le programme sort.
Les appels `gets` vont bloquer les fibers et dire à la boucle événementielle de continuer à partir de là si des données arrivent.

De la même manière, nous pouvons attendre après de multiples fibers d'achever leur exécution, et de regrouper leurs valeurs:

```crystal
channel = Channel(Int32).new

10.times do |i|
  spawn do
    channel.send(i * 2)
  end
end

sum = 0
10.times do
  sum += channel.receive
end
puts sum # => 90
```

Vous pouvez, bien sûr, utiliser `receive` depuis une fiber qui a été lancée:

```crystal
channel = Channel(Int32).new

spawn do
  puts "Avant envoi"
  channel.send(1)
  puts "Après envoi"
end

spawn do
  puts "Avant réception"
  puts channel.receive
  puts "Après réception"
end

puts "Avant yield"
Fiber.yield
puts "Après yield"
```

Affichage:

```text
Avant yield
Avant envoi
Avant réception
Before receive
1
Après réception
Après envoi
Après yield
```

Ici `channel.send` est exécuté en premier, mais étant donné que rien n'est (encore) en attente d'une valeur, l'exécution se poursuit dans les autres fibers.
La seconde fiber est exécutée, il y a une valeur dans le canal, elle est obtenue, et l'exécution continue, d'abord avec la première fiber, puis avec la fiber principale,
parce-que `Fiber.yield` place une fiber en fin de queue d'exécution.

### Canaux tamponnés (Buffered Channels)

Les exemples précédents utilisent des canaux non tamponnés (unbuffered channels):
lors de l'envoi d'une valeur, si une fiber est en attente sur ce canal alors l'exécution continue sur cette fiber.

Avec un canal tamponné (buffered channel), l'appel de `send` ne basculera pas vers une autre fiber à moins que le tampon soit plein:

```crystal
# Un canal tamponné de capacité 2
channel = Channel(Int32).new(2)

spawn do
  puts "Avant envoi 1"
  channel.send(1)
  puts "Avant envoi 2"
  channel.send(2)
  puts "Avant envoi 3"
  channel.send(3)
  puts "Après envoi"
end

3.times do |i|
  puts channel.receive
end
```

Affichage:

```
Avant envoi 1
Avant envoi 2
Avant envoi 3
1
2
Après envoi
3
```

Notez que les deux premiers envois sont exécutés sans basculer vers une autre fiber.
Néanmoins, dans le troisième envoi le tampon du canal est plein, alors l'exécution passe à la fiber principale.
Ici les deux valeurs sont reçues et le canal est épuisé. Au troisième `receive` la fiber principale bloque et l'exécution passe à l'autre fiber,
qui envoie une autre valeur, finit, etc..
