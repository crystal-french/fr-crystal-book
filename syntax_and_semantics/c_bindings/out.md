# out

Intéressons-nous à la fonction [waitpid](http://www.gnu.org/software/libc/manual/html_node/Process-Completion.html):

```crystal
lib C
  fun waitpid(pid : Int32, status_ptr : Int32*, options : Int32) : Int32
end
```

D'après la documentation de la fonction:

```
Les informations de statut du processus fils sont stockées dans l'objet vers lequel status_ptr pointe, à moins que status_ptr soit un pointeur nul.
```

Nous pouvons utiliser cette fonction ainsi:

```crystal
pid = ...
options = ...
status_ptr = uninitialized Int32

C.waitpid(pid, pointerof(status_ptr), options)
```

De cette manière on passe un pointeur `status_ptr` à la fonction pour qu'elle remplisse sa valeur.

Il y a une manière plus simple d'écrire ce qui précéde en utilisant un paramètre `out`:

```crystal
pid = ...
options = ...

C.waitpid(pid, out status_ptr, options)
```

Le compilateur va automatiquement déclarer une variable `status_ptr` de type `Int32`, parce-que l'argument est un `Int32*`.

Cela fonctionnera pour tout type, tant que l'argument est un pointeur de ce type (et, bien sûr, tant que la fonction remplit la valeur vers laquelle pointe le pointeur).
