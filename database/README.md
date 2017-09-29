# Base de donnée

Pour acceder une base de donnée liée vous avez besoin partager le type de  base de donnée du serveur. Le paquet [crystal-lang/crystal-db](https://github.com/crystal-lang/crystal-db) offre une api fonctionnant a travers differents pilotes.

Les paquets suivant sont conforme avec crystal-db

* [crystal-lang/crystal-sqlite3](https://github.com/crystal-lang/crystal-sqlite3) for sqlite
* [crystal-lang/crystal-mysql](https://github.com/crystal-lang/crystal-mysql) for mysql & mariadb
* [will/crystal-pg](https://github.com/will/crystal-pg) for postgres

Ce guide presente l'api de crystal-db, la commande sql peut avoir besoins d'étre addapter pour le pilote utilié à cause des difference qu'il peut y avoir entre les bases de données mysql, postrges et sqlite.

Deplus pilotes peuvent avoir des fonctionalitées additionels comme postgres `LISTEN`/`NOTIFY`.

## Installer les dépendances

Choisisez le bon pilote proposé audessus et ajouter le comme une dépandance dans le fichier `shard.yml`

Il n'y a aucun besoins d'ajouter explicitement la librairie `crystal-lang/crystal-db`

Pendant ce guide `crystal-lang/crystal-mysql` sera utilisé.

```yaml
dependencies:
  mysql:
    github: crystal-lang/crystal-mysql
```

## Se connécté la base de donnée

`DB.open` permet de se connécter avec simplicité en utilisant une connection uri. Le même shema uri determine le pilote attendu. L'exemple suivant se connecte a une base de donnée mysql nomer test avec l'utilisateur root and mot de passe vide.

```crystal
require "db"
require "mysql"

DB.open "mysql://root@localhost/test" do |db|
  # ... use db to perform queries
end
```

Les autre uris connections sont:

* `sqlite3:///path/to/data.db`
* `mysql://user:password@server:port/database`
* `postgres://server:port/database`

Alternativement vous pouvez ne pas utiliser le retoure de `DB.open` tant que `Database#close` est appelé à la fin.

```crystal
require "db"
require "mysql"

db = DB.open "mysql://root@localhost/test"
begin
  # ... use db to perform queries
ensure
  db.close
end
```

## Exec

Pour faire une requette utilisé `Database#exec`

```crystal
db.exec "create table contacts (name varchar(30), age int)"
```

Pour éviter une injection sql utilisé les parametres pour soumetre les données

```crystal
db.exec "insert into contacts values (?, ?)", "John", 30
db.exec "insert into contacts values (?, ?)", "Sarah", 33
```

Note: Quand pilote pg est utilisé, recourez à `$1`, `$2`, etc. à la place de `?`

## Requettes

Pour faire une requette et récuperée le resultat utilisé `Database#query`, des arguements peuvent être utilisés semblablement à `Database#exec`.

`Database#query` retourne un `ResultSet` qui nécesite d'être fermé. Comme `Database#open`, si appelé avec un bloque, le `ResultSet` sera férmé implicitement.

```crystal
db.query "select name, age from contacts order by age desc" do |rs|
  rs.each do
    # ... perform for each row in the ResultSet
  end
end
```

Quand la lecture des valeurs d'une base de donnée s'éffectue il n'y a pas de type d'information pendant le temps de compilation que crystal peut utilisé. Vous devez donc appelé `rs.read(T)` avec le type `T` attendue par le retoure de la requette.

```crystal
db.query "select name, age from contacts order by age desc" do |rs|
  rs.each do
    name = rs.read(String)
    age = rs.read(Int32)
    puts "#{name} (#{age})"
    # => Sarah (33)
    # => John Doe (30)
  end
end
```

Il existes de nombreux aventages à utilisé la méthode `#query`.

Vous pouvez lire plusieurs colones:

```crystal
name, age = rs.read(String, Int32)
```

Ou lire une ligne

```crystal
name, age = db.query_one "select name, age from contacts order by age desc limit 1", as: { String, Int32 }
```

Ou lire une valeur scalar sans passer explicitement par le ResultSet.

```crystal
max_age = db.scalar "select max(age) from contacts"
```

Toutes les methodes utiliser pour les requettes se trouve dans `DB::QueryMethods`.
