Converts a collection to an array.

```foxpro
loCol = CREATEOBJECT("Collection")
FOR lnX =1 TO 100
   loCol.Add(TRANSFORM(lnX))
ENDFOR

DIMENSION laItems[1]
lnCount = CollectionToArray(@laItems, loCol)
? lnCount && 100
```