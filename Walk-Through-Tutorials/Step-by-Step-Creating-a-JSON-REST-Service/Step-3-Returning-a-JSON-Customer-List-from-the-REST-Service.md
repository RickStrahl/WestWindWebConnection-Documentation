Now that we have a working REST service, lets add a new JSON endpoint. To do this we have to add a new method. Let's start with a test request to return a list of customers.

Data can be returned in a number of different ways:

* Simple values (strings, dates, numbers, booleans, binary data)
* Objects including complex objects w/ child objects
* Collections
* Cursors


For more detailed info on what each of these results looks like check out the various [Json serialization formats](VFPS://Topic/_1WU19N5BB).

### Return Cursor Data
Since we're talking about FoxPro here and the most common way to create a 'list' is to create a cursor from a query, the first examples demonstrate how to return a cursor through the REST service. To do this add the following method to the `CustomerRestService` class:

```foxpro
************************************************************************
*  Customers
****************************************
FUNCTION Customers()

SELECT id,firstname,lastname,company,entered FROM Customers ;
      ORDER BY Company ;
      INTO CURSOR TCustomers

RETURN "cursor:TCustomers"
```
or if you're using the business object from the [Business Object Tutorial](VFPS://Topic/_0I102WSAI) instead:

```foxpro
************************************************************************
*  Customers
****************************************
FUNCTION Customers()

loCustBus = CREATEOBJECT("cCustomer")

*** Business object loads TCustomers cursor
lnCount = loCustBus.GetCustomers() 
IF loCustBus.lError
   ERROR loCustBus.cErrorMsg
ENDIF

RETURN "cursor:TCustomers"
```

Note that the syntax used to return a FoxPro cursor or table is a string `"cursor:AliasName"`, which is a special serialization format. When the built in JSON parser sees this format for the result it then serializes the FoxPro cursor into JSON. 

To hit this request in the browser:

<a href="http://localhost/customerdemo/CustomerList.csvc" target="top">http://localhost/customerdemo/CustomerList.csvc</a>

The JSON result from this request looks like this:

```JSON
[
  {
    "id": "_4FG12Y7U6",
    "company": "Avionics Inc.",
    "firstname": "John",
    "lastname": "Doe",
    "entered": "2013-06-12T10:00:00Z"
  },
  {
    "id": "_4FG12Y7TX",
    "company": "Avis World Headquarters",
    "firstname": "Steve",
    "lastname": "Herbin",
    "entered": "2013-09-10T10:00:00Z"
  },
  {
    "id": "_4FG12Y7TV",
    "company": "BGP Productions",
    "firstname": "German",
    "lastname": "Vicencio",
    "entered": "2013-09-30T12:46:40Z"
  }
  ... additional data omitted
]
```

The result is a JSON array of objects with each field mapped to a property of each embedded object.

### Returning Collections
Cursors are one way to return lists, but you can also return list data as collections. wwJsonSerializer supports native FoxPro `Collection` objects or Web Connection `wwCollection` and `wwNameValueCollection` objects for list serialization. Collections are serialized as JSON arrays, but FoxPro array results are not supported because you can't return an array from a method in FoxPro. You can however return an object that has an array property and the nested one-dimensional array will serialize properly

The following code is not really practical but it demonstrates how you can return a list that is a list of objects for our customer list:

```foxpro
************************************************************************
*  Customers
****************************************
FUNCTION Customers()

loCustBus = CREATEOBJECT("cCustomer")

*** Business object loads TCustomers cursor
lnCount = loCustBus.GetCustomers() 
IF loCustBus.lError
   ERROR loCustBus.cErrorMsg  
ENDIF

*** Turn the cursor into FoxPro collection
loCustomers = CursorToCollection("TCustomers")

RETURN loCustomers
```

The JSON result for this is actually identical to the result shown above because in this case, because the collection contains objects that match the fields in the table.

### Capitalization
If you look closely at the returned results you'll notice that all property/field names are returned in lower case. This is because FoxPro doesn't give you proper names when using the built in class and database Reflection APIs (everything is returned in upper case). By default wwJsonSerializer serializes all object properties and field names in lower case.

You can however override this behavior using the [wwJsonSerializer::PropertyNameOverrides](VFPS://Topic/_3FY0SY7K1) property. This property allows to specify a comma delimited list of properties and their properly cased names. The serializer then replaces the generated names with the ones you specify.

To do this you hook `Serializer::PropertyNameOverrides` to override the values:

```foxpro
************************************************************************
*  Customers
****************************************
FUNCTION Customers()

SELECT * FROM Customers ;
      ORDER BY Company ;
      INTO CURSOR TCustomers

*** Override any property names with special case
JsonSerializer.PropertyNameOverrides = "firstName,lastName" 

RETURN "cursor:TCustomers"
```

`JsonSerializer` and also `JsonService` are `PRIVATE` variables that are defined by `wwJsonService` and are passed down to your service method via the call stack. They're in scope inside of any REST process method and allow you to customize the behavior of the serializer and service implementation.

Here we override property names and as you can see I only override the properties that I know need to change - namely the onces that require camelCase which are the 'multi-part' names like `firstName` and `lastName`. When the JSON is renderered it now looks like this:

```foxpro
[
  {
    "company": "Avionics Inc.",
    "entered": "2013-06-12T10:00:00Z",
    "firstName": "John",
    "id": "_4FG12Y7U6",
    "lastName": "Doe"
  },
  {
    "company": "Avis World Headquarters",
    "entered": "2013-09-10T10:00:00Z",
    "firstName": "Steve",
    "id": "_4FG12Y7TX",
    "lastName": "Herbin"
  },
  ...
]
```

and you can see `lastName` and `firstName` properly rendered in the list.

> #### @icon-info-circle JavaScript Naming Conventions
> JavaScript objects returned from services generally use property names that are camel cases, which means they start with lower case letters for the first phrase, and capitalized second phrase. So the `FirstName` table field in the example above becomes `firstName` using camel case. 
> This is a convention and not a requirement, but when you design an API for public consumption try to follow these rules. By using `PropertyNameOverrides` in your API you can easily create properly formatted properties and in most cases there tend to be only a few fields that need custom formatting.