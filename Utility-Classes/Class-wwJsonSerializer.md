This class can serialize FoxPro objects, values, collections and cursors to JSON and deserialize JSON strings into FoxPro objects, values or collections. Arrays are supported only as members of objects - all lists should be expressed preferably as Collections.

The serializer supports complex nested structures and can also serialize (but not directly deserialize) FoxPro tables and cursors by way of a custom string syntax (`cursor:AliasName`). Objects returned are created dynamically on the fly with all arrays parsed into FoxPro collections.

The serializer can handle these FoxPro types:

* Simple Values (strings, numbers, bools, dates, blob)
* Objects - plain or nested
* Arrays
* Collections
* Cursors  (using special `cursor:<cursorName>` syntax)

The de-serializer can handle these JSON types:

* Simple Values (string, number, bool, date, blob)
* Objects - plain or nested
* Arrays - **returned as FoxPro Collections!**
* Cursors - **returned as collections - use [CollectionToCursor()](VFPS://Topic/_45Y0X32PH)**


## Serialization
Serialization takes a FoxPro value, object, collection or cursor and turns it into JSON.

### Serialize Existing Objects or Values

```foxpro
do wwJsonSerializer
loSer = CREATEOBJECT("wwJsonSerializer")
lcJson = loSer.Serialize(loObject)  && Objects, Values, Collections
```

### Simple Value Serialization

```foxpro
loSer = CREATEOBJECT("wwJsonSerializer")

*** Strings are encoded for a number of things which is why it's not a good
*** good idea to generate JSON by hand!
lcJson = loSer.Serialize("Some Long String."+ CHR(13) +CHR(10) + "More Text")
*** Result: "Some Long String.\r\nMore Text"

lcJson = loSer.Serialize(DateTime())  && "2024-01-23T22:44:10Z"
lcJson = loSer.Serialize(.T.)  && true
lcJson = loSer.Serialize(155.55) && 155.55
```

### Simple Object Serialization
You can serialize **any** FoxPro objects or arrays, but you will end up with a lot of extra 'noise' in the object graph with types of `Custom` or `Relation` even, due to FoxPro base class properties which also get serialized.

To get **clean objects** that only hold the properties you create, you can use `EMPTY` objects and explicitly add properties which is what the following examples demonstrate.

```foxpro
loCustomer = CREATEOBJECT("Empty")
ADDPROPERTY(loCustomer,"lastname", "Strahl")
ADDPROPERTY(loCustomer,"firstname", "Rick")
ADDPROPERTY(loCustomer,"company", "West Wind")

loSer = CREATEOBJECT("wwJsonSerializer")
loSer.PropertyNameOverrides = "lastName,firstName"  && exact casing
lcJson = loSer.Serialize(loCustomer, .T.)
```
which produces:

```json
{
  "company": "West Wind",
  "firstName": "Rick",
  "lastName": "Strahl",
}
```

### Nested Objects and Arrays

```foxpro
loCustomer = CREATEOBJECT("Empty")

*** Top level properties
ADDPROPERTY(loCustomer,"lastname", "Strahl")
ADDPROPERTY(loCustomer,"firstname", "Rick")
ADDPROPERTY(loCustomer,"company", "West Wind")

*** Nested object
loAddress = CREATEOBJECT("EMPTY")  
ADDPROPERTY(loCustomer, "address", loAddress)  && Add as child to ^customer
ADDPROPERTY(loAddress, "street", "101 Nowhere Lane")
ADDPROPERTY(loAddress, "city", "Anytown")
ADDPROPERTY(loAddress, "zip", "11111")

*** Nested JSON Array (as FoxPro Collection)
loOrders = CREATEOBJECT("Collection")
ADDPROPERTY(loCustomer,"Orders",loOrders)

*** Add items to Array
loOrder = CREATEOBJECT("EMPTY")
ADDPROPERTY(loOrder,"OrderNo","11111")
ADDPROPERTY(loOrder,"Amount",150.40)
ADDPROPERTY(loOrder,"OrderDate",DATETIME())
loOrders.Add(loOrder)

loOrder = CREATEOBJECT("EMPTY")
ADDPROPERTY(loOrder,"OrderNo","2222")
ADDPROPERTY(loOrder,"Amount",50.55)
ADDPROPERTY(loOrder,"OrderDate",DATETIME())
loOrders.Add(loOrder)

*** A Collection doesn't have to have object members
*** It can also hold simple Values (ie. strings, numbers, dates etc.)
* loOrders.Add("Item 1")
* loOrders.Add("Item 2")

loSer = CREATEOBJECT("wwJsonSerializer")
loSer.PropertyNameOverrides = "lastName,firstName,orderNo,orderDate"  && exact casing
lcJson = loSer.Serialize(loCustomer, .T.)
```

> **Important**: FoxPro objects don't return property names in a case sensitive way, so **by default all property names are serialized as lower case**. If you need mixed case property names, you can use [PropertyNameOverrides](VFPS://Topic/_3FY0SY7K1) to override specific property names with custom casing.

The above produces:

```json
{
  "address": {
    "city": "Anytown",
    "street": "101 Nowhere Lane",
    "zip": "11111"
  },
  "company": "West Wind",
  "firstName": "Rick",
  "lastName": "Strahl",
  "orders": [
    {
      "amount": 150.4,
      "orderDate": "2024-01-23T22:31:17Z",
      "orderNo": "11111"
    },
    {
      "amount": 50.55,
      "orderDate": "2024-01-23T22:31:17Z",
      "orderNo": "2222"
    }
  ]
}
```

### FoxPro Cursors or Tables

```foxpro
select * from customers into TCustomers
lcJson = loSer.Serialize("cursor:TCustomers")  && Tables/Cursors
```
#### Add a FoxPro Cursor as a nested Array

You can also add a cursor as a nested array property in the JSON:

```foxpro
loCustomer = CREATEOBJECT("Empty")

*** regular value property
ADDPROPERTY(loCustomer,"company", "West Wind")

*** Array property from open Cursor TOrders
select * from Orders where custId = lnOrderId INTO CURSOR TOrders
ADDPROPERTY(loCustomer,"orders","cursor:TOrders")   && creates JSON Array
```

> #### @icon-info-circle Cursor Serialization
> A Cursor is **serialized into JSON as an Array of Objects** and **de-serialized from JSON as a FoxPro Collections of Objects**. **It does not automatically de-serialize back into a cursor**!
>
> However, you can use [wwJsonSerializer::DeserializeCursor](VFPS://Topic/_3N51EFR1D) or  [CollectionToCursor()](VFPS://Topic/_45Y0X32PH) to convert JSON collections back into an open cursor or table.


## Deserialization
Deserialization takes a JSON string and turns it into a FoxPro value (for simple types) or an object or collection, depending on the top level JSON structure.

### Deserialization Formats
* JSON Values are mapped to FoxPro Values (ie. string, numbers, bool, dates, bytes, binary)
* JSON Objects are mapped to FoxPro `EMPTY` Objects
* JSON Arrays are mapped to FoxPro `Collection` Objects
* Nested object and array structures are maintained in the generated FoxPro object

The deserializer creates a mapped FoxPro object using values, objects and arrays that map the JSON structure into a FoxPro structure. This allows for objects and arrays to contain other objects and arrays in deeply nested structures.

### Object Deserialization
Here's an example that demonstrates how `wwJsonSerializer` works when round-tripping data:

```foxpro
*** Start with a valid JSON string of a nested object
lcJson = '{ "id": 50445, "message": "Whooo hooo", ' +;
         '   "status": 10 , "entered": "2018-11-21T23:10:10Z", ' +;
         '   "address": { "street": "123 Timber Ln", "number": 32 } }'

*** Deserialize into a FoxPro object
loSer = CREATEOBJECT("wwJsonSerializer")
loResult = loSer.DeserializeJson(lcJson)

? loResult.Id       && 50444
? loResult.entered  && 11/21/2018 03:10 PM (local time)

*** turn the object back into JSON - formatted in this case
? loSer.Serialize(loResult,.T.)
```

The code takes in a JSON string converts it to an object and then turns around and turns it back into formatted JSON which looks like this (formatted because of the `.T.` parameter):

```json
{
  "address": {
    "number": 32,
    "street": "123 Timber Ln"
  },
  "entered": "2018-11-21T23:10:10Z",
  "id": 50445,
  "message": "Whooo hooo",
  "status": 10
}
```

### Arrays Deserialize as Collections
Arrays in JSON are deserialized as FoxPro `Collection` objects so they can be easily iterated using standard `Collection` syntax.

```foxpro
TEXT TO lcJson
[
  {
    "name": "John Doe",
    "company": "Acme Inc",
    "address": {
      "street": "123 Timber Ln"
    },    
    "status": 10
  },
  {
    "name": "Dianne Franken",
    "company": "Dole Inc",
    "address": {
      "street": "312 Northrupp"
    },    
    "status": 11
  }
]
ENDTEXT

LOCAL loItems as Collection

loSer = CREATEOBJECT("wwJsonSerializer")
loItems = loSer.Deserialize(lcJson)

FOR EACH loItem IN loItems FOXOBJECT
   ? loItem.Name
   ? loItem.Address.Street
   ? "----"
ENDFOR

*** Alternately access individual items by index
FOR lnX = 1 TO loItems.Count
	loItem = loItems[lnX]
	? loItem.Name
    ? loItem.Address.Street
    ? "----"
ENDFOR

*** Or this syntax
loItem2  = loItems.Item(2)
? loItem2.Name
```

### Nested Arrays
You can access nested arrays inside of an object in the same way as top level arrays shown in the previous example:

```foxpro
TEXT TO lcJson
{
  "Customers": [
    {
      "name": "John Doe",
      "company": "Acme Inc",    
    },
    {
      "name": "Dianne Franken",
      "company": "Dole Inc",
    }
  ],
  "created": "2014-02-20T00:00:00Z"
}
ENDTEXT

LOCAL loCustomers as Collection

loSer = CREATEOBJECT("wwJsonSerializer")
loResult = loSer.Deserialize(lcJson)  && loResult is an object

loCustomers = loResult.Customers && Array Property

FOR EACH loCustomer IN loCustomers FOXOBJECT
   ? loCustomer.Name
   ? loCustomer.Company
   ? "----"
ENDFOR

*** Or directly
? loResult.Customers[1].Name
```