# Méthodes de macro

Les définitions de macro vous permettent de définir une méthode pour une hiérarchie de classe
qui est ensuite instantiée pour chaque sous-type concret.

Un `def` est implicitement considéré comme une `macro def` s'il contient une expression macro
faisant référence à `@type`. Par exemple:

```crystal
class Object
  def instance_vars_names
    {{ @type.instance_vars.map &.name.stringify }}
  end
end

class Person
  def initialize(@name : String, @age : Int32)
  end
end

person = Person.new "John", 30
person.instance_vars_names #=> ["name", "age"]
```

Vous pouvez aussi rendre cela explicite en écrivant `macro def`,
bien que cela puisse disparaître dans le futur:

```crystal
class Object
  macro def instance_vars_names
    {{ @type.instance_vars.map &.name.stringify }}
  end
end
```

Dans les définitions de macro, les arguments sont passés comme leurs noeuds AST,
vous y donnant accès dans les expansions de macro (`{{some_macro_argument}}`).
Néanmoins ceci est faux pour les définitions de macro. Ici la liste d'arguments
est celle de la méthode générée par la définition de macro.
Vous ne pouvez accéder à leur valeur à la compilation.

```crystal
class Object
  def has_instance_var?(name) : Bool
    # Nous ne pouvons accéder au nom dans l'expansion de la macro ici,
    # à la place nous avons besoin d'utiliser le langage de macro pour construire un array
    # et faire la vérification d'inclusion à l'exécution.
    {{ @type.instance_vars.map &.name.stringify }}.includes? name
  end
end

person = Person.new "John", 30
person.has_instance_var?("name") #=> true
person.has_instance_var?("birthday") #=> false
```
