This class can serialize FoxPro objects, values, collections and cursors to JSON and deserialize JSON strings into FoxPro objects, values or collections. Arrays are supported only as members of objects - all lists should be expressed preferably as Collections.

Here's an example that demonstrates how wwJsonSerializer works when round-tripping data:
```foxpro
lcJson = '{ "id": 50445, "message": "Whooo hooo", ' +;
         '  "status": 10 , "entered": "2018-11-21T23:10:10Z", ' +;
         '  "address": { "street": "123 Timber Ln", "number": 32 } }'

loSer = CREATEOBJECT("wwJsonSerializer")
loResult = loSer.DeserializeJson(lcJson)

? loResult.Id
? loResult.entered

? loSer.Serialize(loResult,.T.)
```

The serializer supports complex nested structures and can also serialize (but not deserialize) FoxPro tables and cursors by way of a custom string syntax (`cursor:AliasName`). Objects returned are created dynamically on the fly with all arrays parsed into FoxPro collections.

**To serialize:**

```foxpro
do wwJsonSerializer
loSer = CREATEOBJECT("wwJsonSerializer")
lcJson = loSer.Serialize(loObject)  && Objects, Values, Collections
```

**To de-serialize:**

```FoxPro
loObject = loSer.DeserializeJson(lcJson)  && JSON Objects, Values, Arrays 
? loObject.Value
```

You can also serialize FoxPro Cursors and Tables using the `cursor:cursorName` syntax:

```foxpro
select * from customers into TCustomers
lcJson = loSer.Serialize("cursor:TCustomers")  && Tables/Cursors
```

You can cannot directly deserialize cursors, but you can use `CollectionToCursor()` to turn the returned collection into a cursor.