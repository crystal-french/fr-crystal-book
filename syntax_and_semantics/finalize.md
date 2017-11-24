# finalize

Si une classe définit une méthode `finalize`,
quand une instance de cette classe passe au ramasse-miette,
cette méthode est appelée:

```crystal
class Foo
  def finalize
    # Appelée lorsque Foo passe au ramasse-miette
    # Permet de libérer des ressources non gérées (c'est-à-dire des bibliothèques C, des structures)
  end
end

Utilisez cette méthode pour libérer les ressources allouées par les bibliothèques externes qui ne sont pas directement géré par le ramasse-miette de Crystal.

Des exemples de ceci peuvent être trouvés dans [`IO::FileDescriptor#finalize`](https://crystal-lang.org/api/IO/FileDescriptor.html#finalize-instance-method)
ou [`OpenSSL::Digest#finalize`](https://crystal-lang.org/api/OpenSSL/Digest.html#finalize-instance-method).

**Note**:

- La méthode `finalize` ne sera invoquée qu'une fois que l'objet aura été
entièrement initialisé via la méthode `initialize`. Si une exception est levée
à l'intérieur de la méthode `initialize`, `finalize` ne sera pas appelé. Si votre classe
définit une méthode `finalize`, assurez-vous d'attraper toutes les exceptions qui pourraient être levé dans les méthodes `initialize` et ressources gratuites.

- L'allocation de toute nouvelle instance d'objet lors de la récupération de place peut entraîner un comportement indéfini et possiblement planter votre programme.
