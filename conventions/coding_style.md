# Style de codage

Ce style est utilisé dans la librairie. Vous pouvez l'utiliser dans vos propres projets pour qu'ils paraissent familier aux autres développeurs.

## Nommage

Les __noms de Type__ sont en casse CamelCase. Par exemple:

```crystal
class ParseError < Exception
end

module HTTP
  class RequestHandler
  end
end

alias NumericValue = Float32 | Float64 | Int32 | Int64

lib LibYAML
end

struct TagDirective
end

enum Time::DayOfWeek
end
```

Les __noms de Méthodes__ sont en case Underscore. Par exemple:

```crystal
class Person
  def first_name
  end

  def date_of_birth
  end

  def homepage_url
  end
end
```

Les __noms de Variables__ sont en case Underscore. Par exemple:

```crystal
class Greeting
  @@default_greeting = "Hello world"

  def initialize(@custom_greeting = nil)
  end

  def print_greeting
    greeting = @custom_greeting || @@default_greeting
    puts greeting
  end
end
```

Les __Constantes__ sont en case Screaming. Par exemple:

```crystal
LUCKY_NUMBERS     = [3, 7, 11]
DOCUMENTATION_URL = "http://crystal-lang.org/docs"
```

### Acronymes

Dans les noms de classe, les acronymes sont _en lettres capitales_. Par exemple, `HTTP`, et `LibXML`.

Dans les noms de méthode, les acronymes sont _en lettres minuscules_.  Par exemple `#from_json`, `#to_io`.

### Libs

Les noms de `Lib` sont préfixés par `Lib`. Par exemple: `LibC`, `LibEvent2`.

### Dossier et Noms de fichiers

Dans un projet:

- `/` contient un fichier readme, toute configuration du projet (i.e., CI ou configuration d'IDE),
  et toute documentation au niveau projet (i.e. changelog ou guide de contribution).
- `src/` contient le code source du projet.
- `spec/` contient les spécifications du projet, qui peut être exécuté avec `crystal spec`.
- `bin/` contient tous les exécutables.

Les chemins de fichier correspondent à l'espace de nommage de leur contenu.
Les fichiers sont nommés après la classe ou l'espace de nommage qu'ils définissent, en _case Underscore_.

Par exemple, `HTTP::WebSocket` est défini dans `src/http/web_socket.cr`.

## Blancs

Utilisez __deux espaces__ pour indenter du code dans des espaces de nom, méthodes, blocs et tout autre contexte imbriqué.
Par exemple:

```crystal
module Scorecard
  class Parser
    def parse(score_text)
      begin
        score_text.scan(SCORE_PATTERN) do |match|
          handle_match(match)
        end
      rescue err : ParseError
        # handle error ...
      end
    end
  end
end
```

Dans une classe, séparez les définitions de méthodes, constantes et définitions de classes internes avec _une nouvelle ligne_.
Par exemple:

```crystal
module Money
  CURRENCIES = {
    "EUR" => 1.0,
    "ARS" => 10.55,
    "USD" => 1.12,
    "JPY" => 134.15,
  }

  class Amount
    getter :currency, :value

    def initialize(@currency, @value)
    end
  end

  class CurrencyConversion
    def initialize(@amount, @target_currency)
    end

    def amount
      # implement conversion ...
    end
  end
end
```
