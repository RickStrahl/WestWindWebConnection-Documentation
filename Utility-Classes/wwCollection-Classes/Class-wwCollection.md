The base wwCollection class allows adding items as single items. The collection is purely accessed by a numeric indexer.

The collection internally manages an aItems array which can also be accessed directly for performance if necessary.

```foxpro
loCol = CREATEOBJECT("wwCollection")

loCol.Add("This is Item1")
loCol.Add("This is Item2")

? loCol.Count           && 2
? loCol.Item(1)         && This is Item1
? loCol.Item(2)         && This is Item2

? loCol.aItems[1]       && This is Item1
? loCol.aItems[2]       && This is Item2


? loCol.Item(4)         && .NULL.

loCol.Remove(2)
? loCol.Count           && 1
```