# pointerof

L'expression `pointerof` retourne un [Pointer](http://crystal-lang.org/api/Pointer.html)
qui pointe vers le contenu d'une variable ou instance de variable.

Un exemple avec une variable:

```crystal
a = 1

ptr = pointerof(a)
ptr.value = 2

a #=> 2
```

Exemple avec une instance de variable:

```crystal
class Point
  def initialize(@x, @y)
  end

  def x
    @x
  end

  def x_ptr
    pointerof(@x)
  end
end

point = Point.new 1, 2

ptr = point.x_ptr
ptr.value = 10

point.x #=> 10
```

Parce-que `pointerof` utilise des pointeurs, elle est considérée non sûre [(unsafe)](unsafe.html).

