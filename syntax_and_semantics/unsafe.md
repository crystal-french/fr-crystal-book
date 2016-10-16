# Code dangereux

Ces aspects du langage sont considérés comme dangereux:

* Code impliquant des pointeurs bruts: le type [Pointer](http://crystal-lang.org/api/Pointer.html) et [pointerof](pointerof.html),
* La méthode de classe [allocate](new,_initialize_and_allocate.html),
* Code impliquant des liaisons C,
* [Déclaration de variable non initialisée](declare_var.html).

"Dangereux" signifie que la corruption de mémoire, les erreurs de segmentation et les crashs peuvent avoir lieu.
Par exemple:

```crystal
a = 1
ptr = pointerof(a)
ptr[100_000] = 2   # comportement indéfini, probablement une erreur de segmentation
```

Néanmoins, du code classique n'implique généralement pas de manipulation de pointeur ou de variables non initialisées.
Et les liaisons C sont généralement encapsulées par des wrappers sûrs qui incluent des vérifications de pointeurs nuls et d'adressage hors limites.

Aucun langage n'est sûr à 100%: certaines parties seront inévitablement bas niveau, interfacées avec le système d'exploitation
et impliqueront la manipulation de pointeur. Mais dès que vous ferez abstraction de cela et travaillerez à un plus haut niveau,
et assumerez (après vérification mathématique ou tests intensifs) que vos fondations sont saines,
vous pourrez être confiant que votre code dans son ensemble sera sûr.
