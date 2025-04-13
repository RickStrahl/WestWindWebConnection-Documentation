So far we've seen how to **publish** the JSON API that allows us to display a list of customers and view, edit, update and delete individual customers. In the real world you'll want to consume the data returned from the API in a real application. The benefit of creating an API is that you can reuse the API across many platforms. So it's possible to call the API from many different client technologies

* Visual FoxPro Desktop or Web Application
* HTML JavaScript Client Application
* Mobile Device Native Application
* **Any application, device or gadget that can send HTTP requests**

In this topic I'll demonstrate how you can use a FoxPro client over the Internet to connect and consume data and interact with this service. In the next step I'll create a small Angular Application that demonstrates using this API from JavaScript.

### Consuming an HTTP API in Visual FoxPro with Web Connection/Client Tools
In order to consume a JSON API you can use Web Connection's client tools (or the West Wind Client Tools) to provide the HTTP access as well as the JSON deserialization functionality to turn the JSON data into something you can use in FoxPro.

Let's start by retrieving the list customer list from the server.

```foxpro
*** Load Libraries and dependencies
DO wwhttp
DO [wwJsonSerializer](VFPS://Topic/_1WU18OWBA)

lcBaseUrl = "http://localhost/customerdemo/"


loHttp = CREATEOBJECT("wwHttp")

*** Returns JSON array
lcJson = loHttp.HttpGet(lcBaseUrl + "customers.csvc")

*** Deserialize customer list into a collection
loSer = CREATEOBJECT("wwJsonSerializer")
loCustList = loSer.DeserializeJson(lcJson)

? loCustList.Count
FOR EACH loCustomer IN loCustList
   ? loCustomer.Company + " " + TRANSFORM(loCustomer.Entered)
ENDFOR
```

First you use the [wwHttp Class](VFPS://Topic/_0JJ1ABF2K) to access the API and retrieve the raw JSON HTTP response. You can then use [wwJsonSerializer](VFPS://Topic/_1WU18OWBA) to deserialize the JSON of the customer list into a collection. You can then iterate over the collection and do as you please with the data.

To retrieve an individual customer, the logic is pretty much the same:

```foxpro
lcCustomerId = "_4FG12Y7UD"

loHttp = CREATEOBJECT("wwHttp")

*** Returns JSON array
lcJson = loHttp.HttpGet(lcBaseUrl + "customer.csvc?id=" + lcCustomerId)

*** Deserialize customer list into a collection
loSer = CREATEOBJECT("wwJsonSerializer")
loCust = loSer.DeserializeJson(lcJson)

? loCust.LastName
? loCust.FirstName
? loCust.COmpany
? loCust.Address
? loCust.Email
? loCust.Entered
? loCust.Updated
```	

### Passing Data to Server
If you need to pass some data to the server, you have to send JSON. To generate that JSON you have a couple of options:

* Generate a raw JSON string in code
* Serialize a value, object or collection

Although it's not very difficult to generate JSON strings in code directly using something like this:

```foxpro
lcName = "Rick"

TEXT TO lcJSON NOSHOW TEXTMERGE
{
  "firstName": "<<lcName>>",
  "lastName": null,
  "company": null
}
ENDTEXT
```
the problem in generating JSON like this is that you may have text formatting that requires special encoding. JSON encodes special characters - quotes, linefeeds, tabs, various punctiation marks and so on in strings, uses a special date format and needs to encode binary data. So while in most cases you can easily generate string data, it doesn't always work out.

The better approach is to create a FoxPro value, object or collection, serialize it into JSON and then pass that.

```foxpro
loParms = CREATEOBJECT("EMPTY")
ADDPROPERTY(loParms,"firstName",lcName)
ADDPROPERTY(loParms,"lastName",lcLastName)
ADDPROPERTY(loParms,"company",null)

loSer = CREATEOBJECT("wwJsonSerializer")
lcJson = loSer.Serialize(loParms)
```

The JSON generated using this approach is guaranteed to be valid, properly encoding strings, dates and other data.

To get our filtered customer list lets try this code:

```foxpro
lcFirstName = "R"
lcLastName = ""

*** Create the Parameters to pass
loParms = CREATEOBJECT("EMPTY")
ADDPROPERTY(loParms,"firstName",lcFirstName)
ADDPROPERTY(loParms,"lastName",lcLastName)
ADDPROPERTY(loParms,"company",null)

loSer = CREATEOBJECT("wwJsonSerializer")
lcJson = loSer.Serialize(loParms)

loHttp = CREATEOBJECT("wwHttp")
loHttp.cHttpVerb = "POST"
loHttp.cContentType = "application/json"

loHttp.AddPostKey(lcJson)

*** Returns JSON array
lcJson = loHttp.HttpGet(lcBaseUrl + "customers.csvc")

*** Deserialize customer list into a collection
loSer = CREATEOBJECT("wwJsonSerializer")
loCustList = loSer.DeserializeJson(lcJson)

? loCustList.Count
FOR EACH loCustomer IN loCustList
   ? loCustomer.Company + " " + loCustomer.FirstName + " " + loCustomer.LastName + " " + TRANSFORM(loCustomer.Entered)
ENDFOR
```

### Receiving Data, Changing and Updating
For updating the customer the process is also very similar. For this next example, we'll load a customer over HTTP, update the customer, then send it back to save on the server:

```foxpro
lcCustomerId = "_4FG12Y7UD"

loHttp = CREATEOBJECT("wwHttp")

*** Returns JSON object
lcJson = loHttp.HttpGet(lcBaseUrl + "customer.csvc?id=" + lcCustomerId)

*** Deserialize customer into an object
loSer = CREATEOBJECT("wwJsonSerializer")
loCust = loSer.DeserializeJson(lcJson)


loCust.FirstName = "Ricky"
loCust.Updated = DATETIME()


lcJson = loSer.Serialize(loCust)

loHttp = CREATEOBJECT("wwHttp")
loHttp.cHttpVerb = "PUT"
loHttp.cContentType = "application/json"	
loHttp.AddPostKey(lcJson)

*** returns updated object
lcJson = loHttp.HttpGet(lcBaseUrl + "customer.csvc")
? lcJson
```

As you can see it's easy to retrieve and update data from a service.

### Consolidate APIs Into a Client Class
My recommendation if you plan on consuming APIs from your application is to create a wrapper class for your service that wraps the service, so your application doesn't embed all that nasty wwHttp and wwJsonSerializer code into the application code. 

Web Connection and the Client Tools provide a handy [wwJsonServiceClient Class](VFPS://Topic/_4JF1F19ZR) that makes this very easy. The class contains a `CallMethod()` method that can make HTTP requests on your behalf and handles serialization and deserialization of the objects to and from JSON as well as handling errors.

Using this class you create nice simple abstraction for your application, that doesn't need to explicitly access wwHttp and wwJsonSerializer:

```foxpro
*********************************************************
DEFINE CLASS CustomerServiceClient AS wwJsonServiceClient
*********************************************************

cServiceBaseUrl = "http://localhost/customerdemo/"


************************************************************************
*  GetCustomers
****************************************
FUNCTION Customers(loParms)
LOCAL loCustomers

loCustomers = THIS.CallService(this.cServiceBaseUrl + "customers.csvc", loParms)

IF THIS.lError
   RETURN null
ENDIF   

RETURN loCustomers
ENDFUNC
*   GetCustomers

************************************************************************
*  GetCustomer
****************************************
FUNCTION GetCustomer(lcId)
LOCAL loCustomer

loCustomer = THIS.CallService(this.cServiceBaseUrl + "customer.csvc?id=" + lcId)
IF THIS.lError
   RETURN null
ENDIF 

RETURN loCustomer
ENDFUNC
*   GetCustomer


************************************************************************
*  UpdateCustomer
****************************************
FUNCTION UpdateCustomer(loCustomer)

loCustomer = THIS.CallService(this.cServiceBaseUrl + "customer.csvc",loCustomer,"PUT")

IF THIS.lError
   RETURN null
ENDIF 

RETURN loCustomer
ENDFUNC
*   UpdateCustomer

ENDDEFINE
*EOC CustomerServiceClient 
```

Each of the methods of this service client map the methods of the service we want to call. But instead of making the HTTP and serialization calls in each of those methods, this code abstracts a reusable **CallService** method that handles these tasks for you in one place. The end result is that your actual service call methods are very simple.

To call this code from your application then becomes very simple:

```foxpro
loService = CREATEOBJECT("CustomerServiceClient")
   
loParms = CREATEOBJECT("EMPTY")
ADDPROPERTY(loParms,"firstName","R")
ADDPROPERTY(loParms,"lastName","")
ADDPROPERTY(loParms,"company",null)

loCustList = loService.Customers(loParms)
if loService.lError
   ? "Couldn't get customers: " + loService.cErrormsg
   RETURN .F.
ENDIF

? loCustList   

FOR EACH loCustomer IN loCustList
   ? loCustomer.Company + " " + loCustomer.FirstName + " " + loCustomer.LastName + " " + TRANSFORM(loCustomer.Entered)
ENDFOR
```	

Notice that this code has no direct dependencies on wwHTTP or wwJsonSerializer, which makes it very easy to all the code. All error handling is also taken care of.

I highly recommend this approach to simplify API consumption.

To be clear, you can use this approach with **any** JSON API service, not just ones you create yourself with Web Connection.