# to_unsafe

Si un type définit une méthode `to_unsafe`, lorsque passé au C la valeur retournée par cette méthode sera passée. Par exemple:

```crystal
lib C
  fun exit(status : Int32) : NoReturn
end

class IntWrapper
  def initialize(@value)
  end

  def to_unsafe
    @value
  end
end

wrapper = IntWrapper.new(1)
C.exit(wrapper) # wrapper n'est pas un Int32, mais sa méthode to_unsafe l'est,
                # alors wrapper.to_unsafe est passée à la place
```

Cela est très pratique pour définir des wrappers de types C sans avoir à les transformer explicitement en leurs valeurs encapsulées.

Par exemple, la classe `String` implémente `to_unsafe` pour retourner `UInt8*`:

```crystal
lib C
  fun printf(format : UInt8*, ...) : Int32
end

a = 1
b = 2
C.printf "%d + %d = %d\n", a, b, a + b
```
