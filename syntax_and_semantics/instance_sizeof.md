# instance_sizeof

L'expression `instance_sizeof` renvoie un `Int32` avec la taille de l'instance d'une classe donnée.
Par exemple:

```crystal
class Point
  def initialize(@x, @y)
  end
end

Point.new 1, 2

# 2 x Int32 = 2 x 4 = 8
instance_sizeof(Point) #=> 12
```

Même si l'instance a deux champs `Int32`,
le compilateur inclut toujours un champ `Int32` supplémentaire pour
l'identifiant de type de l'objet. C'est pourquoi la taille de l'instance est au final de 12 et non 8.
