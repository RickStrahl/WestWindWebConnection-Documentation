A REST service is a fancy way of saying that you are planning on creating an API that serves data rather than HTML. A typical Web Application serves HTML to the browser, which is then rendered and displayed in the browser.

An API doesn't return HTML, but rather returns data. You can think of an API like a FoxPro class, but that's running in the cloud. APIs have data inputs and outputs, and the most common format you use with it is `JSON` and sometimes still `XML`. 

These days `JSON` is the most common data format and that's also the data format that's best supported by Web Connection using the `wwRestService` class. JSON can be easily serialized and deserialized by Web Connection and it is simpler to reason about as JSON has a very small set of valid data types that map closely to FoxPro types. 

### API Data Flow
The idea is:

* A client application makes an HTTP request
* It can optionally pass JSON  data to the server
* The API picks up the incoming data
* The API deserializes the incoming JSON data into a FoxPro object
* The FoxPro object is a single parameter to a FoxPro method
* You implement the method that uses the input object (or value)
* You generate a result
* The result is either a Fox value, object, collection or cursor
* You `RETURN` this value or object
* The API serializes that value in JSON
* The client picks up the JSON and deserializes it
* Life goes on...

### APIs in Web Connection
REST services are literally easy to implement in FoxPro with Web Connection. You create a new service using the Wizard (or manually) by creating a class that inherits from `wwRestService` which is a Web Connection Process class and can be routed to by the Web engine.

You then implement simple method that take a single input parameter - which can be a value, object or collection that's passed by the client. 
You then take this input data value and create a response. A response is either a single value, object, collection or cursor. Note that you can return many values by creating a top level object that wraps multiple child values, objects, collections or cursors.

Here's an example of a very simple API method:

```foxpro
*********************************************************************
FUNCTION GetData(lvParm)
************************

*** Any posted JSON string is automatically deserialized
*** into lvParm FoxPro object or value 

lcSearchFor = lvParm.searchName


*** Simply create objects, collections, values and return them
*** they are automatically serialized to JSON
loObject = CREATEOBJECT("EMPTY")
ADDPROPERTY(loObject,"name","TestPage")
ADDPROPERTY(loObject,"description",;
            "This is a JSON API method that returns an object.")
ADDPROPERTY(loObject,"entered",DATETIME())
ADDPROPERTY(loObject,"searchName",lvParm.searchName)

*** To get proper case you have to override property names
*** otherwise all properties are serialized as lower case in JSON
Serializer.PropertyNameOverrides = "searchName"


RETURN loObject
```

You can return any FoxPro object in this way as a JSON response.

You can also return cursors:

```foxpro
select * from customers into cursor TCustomers

RETURN "cursor:TCustomers"
```

which returns the cursor as an JSON array. You can also attach cursors to an object:

```foxpro
ADDPROPERTY(loObject,"customers","cursor:TCustomers")

RETURN loObject
```

Which returns the object which has a customers array property. 

So there you have the basics. To find out more check out:

* [How wwRestService works](vfps://topic/How%20wwRestService%20works)