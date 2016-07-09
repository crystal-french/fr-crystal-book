# Int

Il y a quatre types d'entiers signés: [Int8](http://crystal-lang.org/api/Int8.html),
[Int16](http://crystal-lang.org/api/Int16.html), [Int32](http://crystal-lang.org/api/Int32.html)
et [Int64](http://crystal-lang.org/api/Int64.html), capables de représenter des nombres de 8, 16, 32 et 64 bits respectivement.

Il y a quatre types d'entiers non signés: [UInt8](http://crystal-lang.org/api/UInt8.html),
[UInt16](http://crystal-lang.org/api/UInt16.html), [UInt32](http://crystal-lang.org/api/UInt32.html)
et [UInt64](http://crystal-lang.org/api/UInt64.html).

Un litéral d'un entier est un signe optionnel `+` ou `-`,
suivi d'une séquence de chifres et soulignés, optionnellement suivis par un suffix.
Sans suffixe, le type du litéral est le plus petit parmi `Int32`, `Int64` et `UInt64`
dans lequel le nombre peut tenir:

```crystal
1      # Int32

1_i8   # Int8
1_i16  # Int16
1_i32  # Int32
1_i64  # Int64

1_u8   # UInt8
1_u16  # UInt16
1_u32  # UInt32
1_u64  # UInt64

+10    # Int32
-20    # Int32

2147483648          # Int64
9223372036854775808 # UInt64
```

Le souligné `_` avant le suffixe est optionnel.

Les soulignés peuvent être utilisés pour rendre certains nombres plus lisibles:

```crystal
1_000_000 # mieux que 1000000
```

Les nombres binaires commencent par `0b`:

```crystal
0b1101 # == 13
```

Les nombres octaux commencent par `0o`:

```crystal
0o123 # == 83
```

Les nombres hexadécimaux commencent par `0x`:

```crystal
0xFE012D # == 16646445
0xfe012d # == 16646445
```
