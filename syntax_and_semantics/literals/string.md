# String

Une [String](http://crystal-lang.org/api/String.html) représente une séquence immuable de caractères UTF-8.

Une String est typiquement créée avec un litéral chaîne de caractères, en encadrant les caractères UTF-8 entre guillemets doubles:

```crystal
"hello world"
```

Vous pouvez utiliser un antislash pour désigner certains caractères spéciaux dans la chaîne:

```crystal
"\"" # guillemet double
"\\" # antislash
"\e" # échappement
"\f" # saut de page
"\n" # nouvelle ligne
"\r" # retour chariot
"\t" # tabulation
"\v" # tabulation verticale
```

Vous pouvez utiliser un antislash suivi par au plus trois chiffres pour représenter un point de code en octal:

```crystal
"\101" # == "A"
"\123" # == "S"
"\12"  # == "\n"
"\1"   # chaîne d'un caractère avec le point de code 1
```

Vous pouvez utiliser un antislash suivi d'un *u* et quatre caractères hexadécimaux pour représenter un point de code unicode:

```crystal
"\u0041" # == "A"
```

Vous pouvez utiliser des accolades pour représenter un hexadécimal jusque 6 nombres (0 à 10FFFF):

```crystal
"\u{41}"    # == "A"
"\u{1F52E}" # == "🔮"
```

Une chaîne peut s'étendre sur plusieurs lignes

```crystal
"hello
      world" # same as "hello\n      world"
```

Notez que dans l'exemple précédent les espaces en début et en fin, ainsi que les nouvelles lignes,
font partie de la chaîne finale. Pour éviter cela, vous pouvez scinder la chaîne
en plusieurs lignes en joignant plusieurs litéraux avec un antislash:

```crystal
"hello " \
"world, " \
"no newlines" # équivalent à "hello world, no newlines"
```

Alternativement, un antislash suivi par une nouvelle ligne peut être inséré dans le litéral:

```crystal
"hello \
     world, \
     no newlines" # équivalent à "hello world, no newlines"
```

Dans ce cas, l'espace en début n'est pas inclus dans la chaîne finale.

Si vous avez besoin d'écrire une chaîne qui a plusieurs guillements doubles,
parenthèses, ou autres caractères similaires, voous pouvez utiliser des
litéraux alternatifs:

```crystal
# Supporte les guillemets doubles et les parenthèses imbriquées
%(hello ("world")) # équivalent à "hello (\"world\")"

# Supporte les doubles guillements et les crochets imbriqués
%[hello ["world"]] # équivalent à "hello [\"world\"]"

# Supporte les guillemets doubles et les accolades imbriquées
%{hello {"world"}} # équivalent à "hello {\"world\"}"

# Supporte les guillements doubles et les chevrons imbriqués
%<hello <"world">> # équivalent à "hello <\"world\">"
```

## Heredoc

Vous pouvez utiliser un "heredoc" pour créer des chaînes de caractères:

```crystal
<<-XML
<parent>
  <child />
</parent>
XML
```

Un "heredoc" est écrit avec `<<-IDENT`, où `IDENT` est un identifiant, une suite de lettres ou nombres
qui doit commencer par une lettre. Le "heredoc" finit sur la ligne qui commence par `IDENT`, ignorant les espaces de début, et il est soit suivi d'un retour à la ligne ou d'un caractère non alphanumérique.


Le dernier point permet d'invoquer des méthodes sur heredocs ou de les utiliser entre parenthèses:

```crystal
<<-SOME
hello
SOME.upcase # => "HELLO"

def upcase(string)
  string.upcase
end

upcase(<<-SOME
  hello
  SOME) # => "HELLO"
```

Les espaces de début sont supprimés du contenu du heredoc en rapport avec le nombre des espaces contenus dans ce dernier `IDENT`.
Par exemple:

```crystal
# Équivalent à "Hello\n  world"
<<-STRING
  Hello
    world
  STRING

# Équivalent à "  Hello\n    world"
<<-STRING
    Hello
      world
  STRING
```

## Interpolation

Pour créer une String incluant des expressions, vous pouvez utiliser l'interpolation de chaîne de caractères:

```crystal
a = 1
b = 2
"sum = #{a + b}"        # "sum = 3"
```

Cela a pour effet d'invoquer `Object#to_s(IO)` sur chaque expression encadrée par `#{...}`.

## Désactiver interpolation et échappements

Pour créer une String sans interpolation ni échappement, utilisez `%q`:

```crystal
%q(hello \n #{world}) # => "hello \\n \#{world}"
```

Les délimiteurs pour `%q(...)` peuvent également être `{}`, `[]` et `<>`.

Un heredoc sans interpolation ni échappement est aussi possible, encadrez simplement le délimiteur heredoc entre guillemets simples:

```crystal
<<-'HERE'
hello \n #{world}
HERE
```
