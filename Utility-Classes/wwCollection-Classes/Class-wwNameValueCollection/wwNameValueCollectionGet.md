Retrieves an item from the collection and returns the item as an Key and Value pair object that has *Key* and *Value* properties.

```foxpro
*** Function that returns a wwNameValueCollection
loVars = GetUrlEncodedValues(lcVars)
FOR lnX = 1 TO loVars.Count
   loVar = loVars.Get(lnX)
   ? loVar.Key + " " + loVar.Value
ENDFOR
```

Note if you are only interested in the value or the key use the `.Item()` method to retrieve just the value, or the `.GetKey()` method to retrieve the key by index. 

This method creates a new object from the results from both of these calls and returns that for you.