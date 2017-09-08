# Serveur HTTP

Un exemple un peu plus intéressant est celui d'un serveur HTTP:

```crystal
require "http/server"

server = HTTP::Server.new(8080) do |context|
  context.response.content_type = "text/plain"
  context.response.print "Hello world! The time is #{Time.now}"
end

puts "Listening on http://127.0.0.1:8080"
server.listen
```

Le code ci-dessus prendra tout son sens quand vous aurez lu toute cette documentation, mais nous pouvons déjà apprendre certaines choses.

* Vous pouvez [utiliser](../syntax_and_semantics/requiring_files.html) du code défini dans d'autres fichiers:

    ```ruby
    require "http/server"
    ```

* Vous pouvez définir des [variables locales](../syntax_and_semantics/local_variables.html) sans avoir à pécifier leur type:

    ```ruby
    server = HTTP::Server.new ...
    ```

* Vous programmez en invoquant des [méhodes](../syntax_and_semantics/classes_and_methods.html) (ou en envoyant des messages) aux objets.

    ```ruby
    HTTP::Server.new(8080) ...
    ...
    Time.now
    ...
    puts "Listening on http://127.0.0.1:8080"
    ...
    server.listen
    ```

* Vous pouvez utiliser des blocs de code, ou plus simplement des [blocs](../syntax_and_semantics/blocks_and_procs.html),
qui sont un moyen pratique de réutiliser du code et d'avoir certaines fonctionnalités du monde de la programmation fonctionnelle:

    ```ruby
    HTTP::Server.new(8080) do |context|
      ...
    end
    ```

* Vous pouvez facilement créer des chaînes de caractères intégrant du contenu, connu sous le nom d'interpolation de chaînes de caractères.
  Le langage possède également d'autres [syntaxes](../syntax_and_semantics/literals.html) pour créer des tableaux, dictionnaires, intervalles, tuples et plus encore:

    ```ruby
    "Hello world! The time is #{Time.now}"
    ```
