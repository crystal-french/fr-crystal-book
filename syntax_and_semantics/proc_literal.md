# Litéral Proc


Un bloc capturé est équivalent à déclarer un [Litéral Proc](literals/proc.html) et le [passer](block_forwarding.html) à la méthode.

```crystal
def some_proc(&block : Int32 -> Int32)
  block
end

x = 0
proc = ->(i : Int32) { x += i }
proc = some_proc(&proc)
proc.call(1)  #=> 1
proc.call(10) #=> 11
x #=> 11
```

Comme expliqué dans la section sur les [litéraux proc](literals/proc.html),
un Proc peut aussi être créé à partir de méthodes existantes:

```crystal
def add(x, y)
  x + y
end

adder = ->add(Int32, Int32)
adder.call(1, 2) #=> 3
```
