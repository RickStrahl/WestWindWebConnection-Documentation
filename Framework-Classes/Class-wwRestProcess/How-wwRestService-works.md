The `wwRestService` uses the  `wwJsonService` class to handle the underlying calling semantics to parse and process JSON requests. wwJsonService is also used for the wwAJaxCallback control.

```
wwProcess
-> Calls wwJsonService.CallMethod() 
-> Calls your Endpoint Method
-> Uses wwJsonSerializer to parse input and generate JSON
```  

This means that your code actually runs inside of the context of wwJsonService class.

### Parameter Parsing
There are a number of ways you can pass parameters to a REST service:

* Query String Values
* POST/PUT Form Variables
* JSON POST/PUT data

QueryString values and Form variables are available just like in any Web Connection process operation. You can use Response.QueryString() or Request.Form() to retrieve any values that are passed. This works just as you would expect.

```foxpro
FUNCTION GetAlbum
lnPk = VAL(Request.QueryString("id"))

loAlbum = CREATEOBJECT("cAlbum")
IF !loAlbum.Load(lnPk)
   ERROR loAlbum.cErrorMsg
ENDIF

RETURN loAlbum.oData
ENDFUNC
```

Optionally you can post a single JSON object or value to the server and that object is parsed into a single parameter FoxPro parameter. The parsing is done via wwJsonSerializer::DeserializeJson() which produces a FoxPro object. Any JSON arrays are turned into FoxPro collections.

```foxpro
*** JSON POST data parsed to loAlbum parameter
FUNCTION SaveAlbum(loAlbum)

IF ISNULL(loAlbum)
   ERROR "Invalid album data."
ENDIF

loAlbum = CREATEOBJECT("cAlbum")
loAlb = loAlbum.oData

loAlb.Descript = loAlbum.Descript
loAlb.ImageUrl = loAlbum.ImageUrl

IF !loAlbum.Save()
   ERROR loAlbum.cErrorMsg
ENDIF

RETURN loAlb
```
### In Scope Variables
A number of Scope variables are passed into your method. 

* **JsonService**  
An instance of the wwJsonService class that you can manipulate. This is useful to override behavior of the service, such as telling it to not generate any output using the [IsRawResponse property](vfps://Topic/_3W317ZAT5) which you can use if you want to use the Response object to return your own output. Use this to return non-data results such as images, documents or occasional HTML from the service. This avoids 

* **Serializer**  
An instance of the wwJsonSerializer which allows you to override behavior of the serializer. For example, you can override things like the [PropertyNameOverrides property](vfps://Topic/_3FY0SY7K1) to generate 'proper' field names rather than all lowercase.

* **Request, Response, Server, Process**  
The intrinsic Web Connection objects are also available inside of a REST call.


### Output Generation
The default output mechanism for wwRestProcess is JSON. If no Accept header is specified the output will be JSON. 

You can override the output by providing an Accept header that contains either:

* application/json
* text/xml

Any output you return from your wwRestProcess method is turned into JSON or XML and the appropriate content is set.

Any error that occurs is captured and turned into an error object and returned in the JSON or XML as well. The returned object contains isCallBackError and message properties.

The easiest way to return error results is to use the ERROR command to throw an error and message.

```foxpro
FUNCTION GetItem(sku)

loBusItem = CREATEOBJECT("wwBusItem")
IF !loBusItem.Load(sku)
    ERROR loBusItem.cErrorMsg
ENDIF

RETURN loBusItem.oData
ENDFUNC
```