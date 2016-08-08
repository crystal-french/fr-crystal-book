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

# Ecrit "Bye bye ...!" indéfiniment
loop do
  Foo.new
end
```

**Note:** La méthode `finalize` sera seulement appelée quand l'objet a été complétement initialisé via la méthode `initialize`.
Si une exception est levée dans la méthode `initialize`, `finalize` ne sera pas appelée.
Si votre classe définit une méthode `finalize` assurez-vous de récupérer toutes les exceptions qui pourraient être
levées dans les méthodes `initialize` et de libérer les ressources.
