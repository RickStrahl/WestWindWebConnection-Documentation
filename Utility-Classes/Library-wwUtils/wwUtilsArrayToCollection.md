Convert a FoxPro array to a Collection.

Useful in scenarios where you need to return a 'collection' result but you only have access to an array. Arrays can't be passed from methods and don't pass well over COM, so they work badly in multi-tier environments for serialization or for passing to external applications. Collections are more flexible and this function makes it easy to convert arrays to something more usable.

```foxpro
DIMENSION laArray[100] 
FOR lnX =1 TO 100
   laArray[lnX ] = TRANSFORM(lnX)
ENDFOR

loCol = ArrayToCollection(@laArray)
? loCol.Count  && 100
```