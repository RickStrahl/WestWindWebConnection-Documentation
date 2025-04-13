The wwJSONSerializer class serializes FoxPro values in JSON formatted strings. This topic demonstrates how to create output for various types and what the output looks like:

### String
Let's start with a simple string serialized into JSON:

```foxpro
DO wwJsonSerializer && Load Libs
loSerializer = CREATEOBJECT("wwJsonSerializer")
lcJSON = loSerializer.Serialize("Hello Rick")
```

returns: `"Hello Rick"` (including the quotes)

### Dates
Dates are serialized and parsed in ISO format which looks like this:

```foxpro
loSerializer = CREATEOBJECT("wwJsonSerializer")
lcJSON = loSerializer.Serialize(DateTime())
```
returns: `"2011-01-08T04:34:56Z"`

There's also an option flag that supports Microsoft's old MSAJAX style serialization format.

### Numbers and Booleans
All other simple types are created using standard JavaScript literals for types. Numbers are created as simple number values (decimal style). Booleans are stored as true and false.

### Binary Values
Binary values are serialized as **Base64 string** which is standard for JSON data.

However, when deserializing binary data from JSON the value is returned as a **Base64 String** value. To get the binary value, you have to **explicitly convert** the value from string to binary using `STRCONV(lvData, 14)`.

```foxpro
loObj = CREATEOBJECT("EMPTY")
ADDPROPERTY(loObj,"Name", "Rick")
ADDPROPERTY(loObj,"Data", CAST( "ASDAS" as BLOB))

loSer = CREATEOBJECT("wwJsonSerializer")
lcJson = loSer.Serialize(loObj)
? lcJson                    && {"data": "QVNEQVM=","name": "Rick"}

loObj2 = loSer.Deserialize(lcJson)
? loObj2.Name               && regular string
? loObj2.Data               && base64 string: QVNEQVM=
? STRCONV(loObj2.Data,14)   && ASDAS
```

### Objects
You can also serialize objects. Here's an example of a hierarchical object:

```foxpro
loCustomer = CREATEOBJECT("EMPTY")
ADDPROPERTY(loCustomer,"Company","West Wind Technologies")
ADDPROPERTY(loCustomer,"Name","Rick Strahl")
ADDPROPERTY(loCustomer,"Entered",DATETIME())

loAddress = CREATEOBJECT("EMPTY")
ADDPROPERTY(loAddress,"Street","32 Kaiea")
ADDPROPERTY(loAddress,"City","Paia")
ADDPROPERTY(loAddress,"State","HI")
ADDPROPERTY(loAddress,"Zip","96779")
ADDPROPERTY(loAddress,"Occupants",1)

ADDPROPERTY(loCustomer,"Address",loAddress)

loSerializer = CREATEOBJECT("wwJsonSerializer")
loSerializer.FormattedOutput = .T.  && prettify the JSON
lcJSON = loSerializer.Serialize(loCustomer)
```

which yields:

```JSON
{
  "address": {
    "city": "Paia",
    "occupants": 1,
    "state": "HI",
    "street": "32 Kaiea",
    "zip": "96779"
  },
  "company": "West Wind Technologies",
  "entered": "2015-10-27T22:41:17Z",
  "name": "Rick Strahl"
}
```

> ### @icon-info-circle Formatted Output
>Note that this is **formatted** output which is created when the `.FormattedOutput` property is `true`. By default the serializer produces **unformatted** output, which is a single line of text.

All properties are created inside of JavaScript object notation ({ }) and an inner object is created for the address. As you can see JSON is self-describing in that there's no schema required - the format lets JavaScript know what type you're dealing with. 

### Proper Case and Customized Property Names
By default all object properties from FoxPro are rendered in **lower case** due to FoxPro's inability to return properly cased generic runtime type information.  

You can optionally override individual property names generated using the 
**[.PropertyNameOverrides](vfps://Topic/_3FY0SY7K1)** property.

```foxpro
loSerializer = CREATEOBJECT("wwJsonSerializer")

loSerializer.FormattedOutput = .T.
loSerializer.PropertyNameOverrides = ;
  "Address,City,Occupants,State,Street,Zip,Company,Entered,Name"

lcJSON = loSerializer.Serialize(loCustomer)

```

Simply create a comma delimited string that includes all the property names you want to override, using the proper case you'd like to see. Note that you only need to provide those properties that need to be overwritten. 

Internally this replaces all the lower case properties with the explicitly provided properties:

```JSON
{
  "Address": {
    "City": "Paia",
    "Occupants": 1,
    "State": "HI",
    "Street": "32 Kaiea",
    "Zip": "96779"
  },
  "Company": "West Wind Technologies",
  "Entered": "2015-10-27T22:43:02Z",
  "Name": "Rick Strahl"
}
```
Keep in mind that most JavaScript objects are expected to be camelCased - meaning lower case first word with upper cased subsequent words (ie. camelCase, birthDate, firstName, propertyNameOverrides etc.) so typically you need to apply name overrides only only longer, multi-part words. 

#### Mapping Properties to Custom Property Names
Sometimes transforming the case of a property isn't enough and you need to produce a property (or Map name) that can't be created via a FoxPro property name. For example, properties that start with a number, contain spaces or extended characters or other any number of other naming scenarios that FoxPro properties doesn't support.

You can use [.MapPropertyName()](VFPS://Topic/_67X0KWDFD) to transform any property name in a JSON string to **any other name**. The example shows how to create property/map names with spaces:

```foxpro
loSer = CREATEOBJECT("wwJsonSerializer")
loObj = CREATEOBJECT("EMPTY")
ADDPROPERTY(loObj,"LastName","Strahl")
ADDPROPERTY(loObj,"FirstName","Rick")

*** Create initial JSON
*** { "lastname": "Strahl", "firstname": "Rick" }
lcJson = loSer.Serialize(loObj)

*** Now update the property names
loSer.MapPropertyName(@lcJson, "lastName","Last Name")
loSer.MapPropertyName(@lcJson, "firstName","First Name")

*** { "Last Name": "Strahl", "First Name": "Rick" }
? lcJson
```

### Arrays, Collections and wwCollection objects
wwJsonSerializer serializes FoxPro arrays, Collections and wwCollection objects into JSON arrays. Key Value collections or wwKeyValue collections are rendered as JSON object maps where the keys are mapped to JSON properties of the object. 

Array elements can be of any other type supported including object and other arrays. Arrays can be of a single type or mixed types as well.

Arrays are serialized as FoxPro Collections, which have a count property and an Item(x) method to retrieve items.

Here's a simple string array:
```foxpro
lcJson = '["Value,1","Value2. This \"is\" neat","Value3"]'
? lcJson
loResult = loJson.Deserialize(lcJson)

*** Result is a Collection
? loResult.Count
FOR lnX = 1 TO loResult.Count
   ? loResult.Item(lnX)
ENDFOR

? "*** Re-serialize String collection"
? loJson.Serialize(loResult)  && Same as original string
```

Another example using an object array:

```foxpro
? "*** Object array"
lcJson = '[{"name":"Rick","company":"West Wind","Balance":100.05},' + ;
         '{"name":"Markus","company":"EPS Software","Balance":1100.05},' +;
         '{"name":"Kevin","company":"OakLeaf","Balance":1300.05},' 
loResult =loJson.Deserialize(lcJson)

? loResult.Count
FOR lnX = 1 TO loResult.Count
   loCust = loResult.Item(lnX)
   ? loCust.name, loCust.company, loCust.Balance
ENDFOR

? "*** re-serialize object array"
? loJson.Serialize(loResult)  && same as above
```

You can also have arrays with mixed items: simple values, objects and even other objects.

```foxpro
? "*** Mixed items"
lcJson = '["Value" , {"name":"Rick","company":"West Wind"} , ["rick","markus"],"Test",10,null,"Test1"]'
?lcJson
loResult =loJson.Deserialize(lcJson)
? loResult.Count
FOR lnX = 1 TO loResult.Count
   ? loResult.Item(lnX)
ENDFOR

? "*** Re-serialize loResult"
? loJson.Serialize(loResult)
```


### FoxPro Cursors or Tables
You can also serialize cursors, which are rendered as JSON arrays. These cursor arrays can also be attached as child arrays to objects.

To serialize a cursor you use a special syntax in the call to Serialize by using `cursor:Alias`:

```foxpro
SELECT * FROM customers ;
   INTO CURSOR TCompanies ;
   ORDER BY company

loSerializer = CREATEOBJECT("wwJsonSerializer")
loSerializer.FormattedOutput = .T.
loSerializer.PropertyNameOverrides = "firstName,lastName,shipAddr,billRate"

*** Notice the 'cursor:alias' syntax
lcJSON = loSerializer.Serialize("cursor:TCompanies")
```

The result of this serialization looks like this:

```foxpro
[
  {
    "id": "_4FG12Y7U6",
    "firstName": "John",
    "lastName": "Doe",
    "company": "Avionics Inc.",
    "careof": "John Doe",
    "address": "PO Box 1907\r\nManassas, VA 22110-0801",
    "shipAddr": "",
    "email": "",
    "phone": "808 123-0001",
    "billRate": 150,
    "btype": "Labor_PRG",
    "entered": "2013-06-12T09:46:40Z",
    "state": ""
  },
  {
    "id": "_4FG12Y7TX",
    "firstName": "Steve",
    "lastName": "Herbin",
    "company": "Avis World Headquarters",
    "careof": "Steve Herbin",
    "address": "111 Old Country Road\nGarden City, NY 11530\n",
    "shipAddr": "",
    "email": "",
    "phone": "(516) 222-3743",
    "billRate": 55,
    "btype": "Labor_PRG",
    "entered": "2013-09-10T09:46:40Z",
    "state": ""
  },
 {
    "id": "_4FG12Y7TQ",
    "firstName": "Dan",
    "lastName": "Mullen",
    "company": "XVC Solutions",
    "careof": "Dan Mullen",
    "address": "22xx Tudor Lane\r\nStow, OH 44xx",
    "shipAddr": "",
    "email": "",
    "phone": "",
    "billRate": 80,
    "btype": "Labor_PRG",
    "entered": "2013-10-19T09:46:40Z",
    "state": ""
  },
  {
    "id": "_4FG12Y7U4",
    "firstName": "Kim",
    "lastName": "Westin",
    "company": "XYZ Systems house",
    "careof": "",
    "address": "9411 Broadway\r\nNew York, NY 11111",
    "shipAddr": "",
    "email": "",
    "phone": "444-4444",
    "billRate": 150,
    "btype": "Labor_PRG",
    "entered": "2013-07-02T09:46:40Z",
    "state": ""
  }
]
```

As with object output, you can use `.PropertyNameOverrides` to override the names and case of the property names.

> #### Legacy Format
> The format of the default `cursor:cursorName` rendering has changed to return just the array. The legacy format returned a top level object with `Rows` array and `Count` property. You can still use this format by using `cursor_legacy:cursorName`.

#### Mixing Objects and Cursors
You can also **nest cursors into objects** by attaching cursors as *'properties'* to an object. In FoxPro you can reference the cursor by name using `cursor:<alias>` as the value assigned to the property. When the object is serialized the serializer then serializes the cursor as a object array at the property.

An example of this is to create a custom JSON response for something like jqGrid, which requires an object structure and an array structure for the data to display. The following code demonstrates how to create this structure:

```foxpro
SELECT Company, Careof FROM wwDemo\tt_cust INTO CURSOR TPersons

loGrid = CREATE("EMPTY")

AddProperty(loGrid,"page",1)
AddProperty(loGrid,"total", Reccount())
AddProperty(loGrid,"records",10)

*** Attach the cursor as an Array property
AddProperty(loGrid,"rows","cursor:TPersons")

loSer = CREATEOBJECT("wwJsonSerializer")
loSer.FormattedOutput = .T.
lcJSON = loSer.Serialize(loGrid)

ShowText(lcJson)
```

This creates a structure like this:

```json
{
   page: 1,
   total: 100,
   records: 10,
   rows: [
        {
            company: "West Wind",
            careof: "Rick"
        },
        {
            company: "EPS",
            careof: "Markus"
        }
   ]
}
```

Note the rows property and the assignment of `cursor:TQuery` which generates just the array in the object without the Rows object prefix.


#### No built-in Two Way Cursor Serialization
The JSON `.Deserialize()` method only works with objects and can't deserialize cursors directly. While you can **serialize** cursors, there's no way to turn JSON back into a cursor. Because a cursor is serialized as a JSON array, when you deserialize the JSON array you get back a FoxPro collection.

There are a couple of ways however that you can deserialize cursors explicitly:

* `.DeserializeCursor()`
* Use `CollectionToCursor()` to convert a result collection to cursor

#### DeserializeCursor
This method is a special helper method that works on top level cursors - or object arrays - in the JSON data. This method is essentially a wrapper around `.Deserialize()` and `CursorToCollection()`.

To use it you pass in the top level JSON containing an object array (cursor data) and the name of an open cursor that contains the proper structure to import to.

```foxpro
DO wwJsonSerializer

loSer = CREATEOBJECT("wwJsonSerializer")

*** Some data to serialize
SELECT TOP 5 * FROM Customers ORDER BY Company INTO CURSOR TQuery

CLEAR
lcJson = loSer.Serialize("cursor:TQuery", .T.)
? lcJson

USE IN TQuery && close

*** Create an empty writable cursor as 'schema' to import to
SELECT * FROM Customers WHERE 1=0 INTO CURSOR TCustomers READWRITE

* Collection of cust objects
? loSer.DeserializeCursor(lcJson, "TCustomers")

BROWSE NOWAIT
```

#### CollectionToCursor()
As mentioned the JSON created from a serialized cursor is a JSON Object Array, and when you use standard de-serialization via `DeSerialize()` the result is a collection object. You can take the collection result and run it through the `CollectionToCursor()` library function (in wwutils.prg) to convert the collection to a cursor, by providing an open  cursor into which to import the data. The open cursor's schema determines which fields and types that are imported into the cursor, matching JSON object properties to cursor field names.

To do this manually in FoxPro code looks like this:

```foxpro
*** Some data to serialize
SELECT * FROM customers  INTO CURSOR TCompanies

*** Capture JSON from cursor: JSON Array
lcJSON = loSerializer.Serialize("cursor:TCompanies")

CLOSE DATA

*** Deserialize JSON Array into Collection
loCol = loSerializer.Deserialize(lcJson)

*** Schema:  Create an empty, writable cursor to import to
SELECT * from Customers ;
       INTO Cursor TCompanies READWRITE ;
       WHERE .f.   

*** Import data from the Collection
CollectionToCursor(loCol,"TCompanies")

BROWSE NOWAIT
```

While `DeserializeCursor()` is easier to use and simply a wrapper around `Deserialize()` and `CollectionToCursor()`, this manual approach allows a lot more control. For example, you can apply this approach **to any collection including child object properties** in a nested object returned from JSON parsing (ie. import from `Invoice.LineItems[]`).