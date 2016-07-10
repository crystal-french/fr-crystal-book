# Range

Une [étendue](http://crystal-lang.org/api/Range.html) est typiquement construite avec un litéral d'étendue:

```crystal
x..y  # une étendue inclusive, en mathématiques: [x, y]
x...y # une étendue exclusive, en mathématiques [x, y)
```

Un moyen simple de se souvenir quelle forme est inclusive ou exclusive est de se dire que le point supplémentaire
repousse *y*, le laissant ainsi en dehors de l'étendue.
