# Constantes

Vous pouvez également déclarer des constantes dans une déclaration `lib`:

```crystal
@[Link("pcre")]
lib PCRE
  INFO_CAPTURECOUNT = 2
end

PCRE::INFO_CAPTURECOUNT #=> 2
```
