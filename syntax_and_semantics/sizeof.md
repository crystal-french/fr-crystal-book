# sizeof

L'expression `sizeof` renvoie un `Int32` avec la taille en bytes du type donné. Par exemple:

```crystal
sizeof(Int32)  #=> 4
sizeof(Int64)  #=> 8
```

Pour les types [Référence](http://crystal-lang.org/api/Reference.html),
la taille est la même que celle du pointeur:

```crystal
# On a 64 bits machine
sizeof(Pointer(Int32)) #=> 8
sizeof(String)         #=> 8
```

Ceci à cause de la mémoire d'une Référence qui est allouée sur le tas et un pointeur vers celle-ci est fournie.
Pour avoir la taille réelle d'une classe, utilisez [instance_sizeof](instance_sizeof.html).

L'argument de sizeof est un [type](type_grammar.html) et est souvent combiné avec [typeof](typeof.html):

```crystal
a = 1
sizeof(typeof(a)) #=> 4
```
