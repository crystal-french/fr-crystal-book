# finalize

Si une classe définit une méthode `finalize`,
quand une instance de cette classe passe au ramasse-miette,
cette méthode est appelée:

```crystal
class Foo
  def finalize
    # Appelée lorsque Foo passe au ramasse-miette
    puts "Bye bye from #{self}!"
  end
end

Utilisez cette méthode pour libérer des ressources allouées par des bibliothèques externes qui ne sont
pas directement géré par le collecteur d'ordures Crystal.

Des exemples de ceci peuvent être trouvés dans [`IO::FileDescriptor#finalize`](https://crystal-lang.org/api/IO/FileDescriptor.html#finalize-instance-method)
ou [`OpenSSL::Digest#finalize`](https://crystal-lang.org/api/OpenSSL/Digest.html#finalize-instance-method).

**Note**:

- La méthode `finalize` sera seulement appelée quand l'objet a été complétement initialisé via la méthode `initialize`.
Si une exception est relevée dans la méthode `initialize`, `finalize` ne sera pas appelée.
Si votre classe définit une méthode `finalize` assurez-vous de récupérer toutes les exceptions qui pourraient être
levées dans les méthodes `initialize` et de libérer les ressources.

- L'attribution de toute nouvelle instance d'objet lors de la collecte des ordures pourrait en résulter
dans un comportement indéfini et très probablement bloquant votre programme.