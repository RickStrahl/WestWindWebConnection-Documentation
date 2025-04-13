Deserializes a top level JSON object array into an open FoxPro cursor or table.

The cursor or table **has to already exist and must be open** so that data can be loaded into it from the JSON. The open cursor serves as the 'schema' to figure out which fields to map into the table's columns. Only top level properties are mapped to any cursor/table.

JSON has no concept of 'cursors' and holds columnar data as **Arrays**. When you serialize cursors, they are serialized into JSON as **Object Arrays**. But when that same data is deserialized back into FoxPro, it's returned as a `Collection` of objects, not a cursor since there's no context that the array is a cursor, nor any sort of mapping schema information.

To work around this problem this helper method combines `Deserialize()` and [CollectionToCursor()](VFPS://Topic/_45Y0X32PH) to deserialize a top level JSON array into an open cursor that matches the structure of the object data of the JSON Array.

```foxpro
DO wwJsonSerializer

loSer = CREATEOBJECT("wwJsonSerializer")

SELECT TOP 5 * FROM Customers ORDER BY Company INTO CURSOR TQuery

lcJson = loSer.Serialize("cursor:TQuery", .T.)
? lcJson

USE IN TQuery && close cursor

*** Now create a new empty writable cursor as 'schema' to import to
SELECT * FROM Customers WHERE 1=0 INTO CURSOR TCustomers READWRITE

*** returns 5 and TCustomers now has 5 added customers
? loSer.DeserializeCursor(lcJson, "TCustomers")

BROWSE NOWAIT
```

> This method only works with a **top level object JSON Object Array** and does not work with nested child properties. The assumption is that the entire result is the table data contained in a single array of objects where the object matches the cursor/table fields.
> 
> For de-serializing child cursors from JSON array properties, deserialize the entire object first, then explicitly use `CollectionToCursor()` on the Collection property that you want to de-serialize into a cursor.