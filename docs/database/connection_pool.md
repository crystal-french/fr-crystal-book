# Stoquage de connection

En générale une connection implique ouverture d'une connection TCP ou Socket. La Socket gére une requette à la fois. Quand un programme a besoin d'executer plusieurs requêtes de manières symultanées, ou si il manie des requêtes concurentes qui a pour butte d'utiliser les bases de données, il aura besoins de plusieurs connections activse.

Par ailleurs comme les bases de données son des servises séparés de l'application qui les utilisés, il peut y avoir une déconnexion, le services peut être relancé, ainsi que d'autres chauses dont le programme n'a pas besoins de savoir.

Le stoquage connection est géneralement la solution la plus satisfesante a ce probléme.

Quand une base de donnée est ouverte avec `crystal-db` un stoquage de connéctions existe déjà. `DB.open` retourne un `DB::Database` object qui gére le stoquage de connéction et pas uniquement la connéction en elle-même .

```crystal
DB.open("mysql://root@localhost/test") do |db|
  # db is a DB::Database
end
```

Quand une requête est envoyée avec `db.query`, `db.exec`, `db.scalar`, etc. l'algorithme s'éxecute de la maniére suivante:

1. Trouve une connéction libre dans le stoquage.
   1. Crée s'en une si besions ou possible.
   2. Si le stoquage n'autorise pas la création d'une nouvelle connection, attendre qu'une connection se libére.
      1. Abondonner si l'attente est trop longue.
2. Vérifier si la connéction c'est bien éffectuée.
3. Executé la commande SQL.
4. Si il n'y a pas de retoure de `DB::ResultSet`, retourner la connéction au stoquage. Sinon, la connéction retournera le stoquage qunad le ResultSet est férmé.
5. Retourne le code de status.

Si la connéction ne peux pas étre faite, ou si la connéction est perdue lors d'une requête les étapes précedntes son répétées.

> L'iteration de l'algorithme se fait seulement si la demade est par `DB::Database`. Si la requette est faite à travers `DB::Connection` ou `DB::Transaction` le procédé n'est pas répeter puisque le code spécifira que une certaine connection était attendue.

## Configuration

Le comportement du stoquage des connéctions peut être configuré depuis l'initialisations de paramétres peuvent prendre le format d'une requête sous forme de chaine de charactère dans la connéction de l'URI.

| Name | Default value |
| :--- | :--- |
| initial\_pool\_size | 1 |
| max\_pool\_size | 0 \(unlimited\) |
| max\_idle\_pool\_size | 1 |
| checkout\_timeout | 5.0 \(seconds\) |
| retry\_attempts | 1 |
| retry\_delay | 1.0 \(seconds\) |

Quand `DB::Database` est ouvert `initial_pool_size` un nombre de connections serons crées par default. Le stoquage ne contiendra jamais plus de `max_pool_size` connections. Quand une connection est donné au stoquage et que le `max_idle_pool_size` est atteint la connéction tourne au ralentie.

Si le `max_pool_size` est atteint est que que une connéction est demandée, attendre jusqu'a `checkout_timeout` seconds ou jusqu'a une connéction se libére.

Si une connection est perdue ou ne peut pas s'éffectuer alors réessayer apres `retry_attempts` temps d'attente `retry_delay` seconds apres chaque essay.

## Échantillon

Le programme suivant affiche l'heure local mais si al connéction est perdue ou que tous les serveurs son hors service pendant quelques secondes le programme continuera a tourner sans erreur.

```crystal
# file: sample.cr
require "mysql"

DB.open "mysql://root@localhost?retry_attempts=8&retry_delay=3" do |db|
  loop do
    pp db.scalar("SELECT NOW()")
    sleep 0.5
  end
end
```

```
$ crystal sample.cr
db.scalar("SELECT NOW()") # => 2016-12-16 16:36:57
db.scalar("SELECT NOW()") # => 2016-12-16 16:36:57
db.scalar("SELECT NOW()") # => 2016-12-16 16:36:58
db.scalar("SELECT NOW()") # => 2016-12-16 16:36:58
db.scalar("SELECT NOW()") # => 2016-12-16 16:36:59
db.scalar("SELECT NOW()") # => 2016-12-16 16:36:59
# stop mysql server for some seconds
db.scalar("SELECT NOW()") # => 2016-12-16 16:37:06
db.scalar("SELECT NOW()") # => 2016-12-16 16:37:06
db.scalar("SELECT NOW()") # => 2016-12-16 16:37:07
```
