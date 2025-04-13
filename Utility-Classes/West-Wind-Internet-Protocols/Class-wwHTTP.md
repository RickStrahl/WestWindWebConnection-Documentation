This class can be used to make HTTP requests to Web servers, to retrieve data. You can also send data to the server using POST or PUT operations. All HTTP verbs are supported.

At it's simplest you can retrieve HTTP content like this:

```foxpro
loHttp = CREATEOBJECT("wwHttp")
lcHtml = loHttp.Get("https://markdownmonster.west-wind.com")
```

Content retrieved can also be saved to a file directly using the `lcOutputName` parameter of all the retrieval routines (`.Get()`, `.Post()`, `.Put()`, `.Delete()`):

```foxpro
loHttp = CREATEOBJECT('wwhttp')
loHttp.Get("https://west-wind.com/files/wwclient.zip","c:\temp\wwclient.zip")
```

You can also POST data to the server using the `.AddPostKey()` method which lets you post either individual form values or a complete POST buffer to the server.

POST some data to a server using HTML style form vars:

```foxpro
oHTTP=CREATEOBJECT("wwHTTP")

oHTTP.AddPostKey("FirstName","Rick")
oHTTP.AddPostKey("LastName","Strahl")
oHTTP.AddPostKey("Company","West Wind Technologies")

*** Optionally add custom headers
oHTTP.AppendHeader("cache-control","private")
oHttp.AppendHeader("Custom-Header","Custom value")

lcHTML = oHTTP.Post("http://www.west-wind.com/wconnect/TestPage.wwd")

ShowHTML( lcHTML )
```

alternately you can post an entire buffer of data to the server. This is common when you sent JSON or XML documents:

```foxpro
loHttp = CREATEOBJECT("wwHttp")

*** Specify that we want to post raw data and a custom content type
loHttp.cContentType = "application/json"  && Content type of the data posted
* loHttp.cHttpVerb = "POST"       && Use for anything but POST/GET verbs (PUT/DELETE/HEAD/OPTIONS)

*** Load up the XML data any way you need
lcJson = FILETOSTR("Data.json")

*** New simpler syntax for raw buffers
lcJsonResult = loHttp.Post("http://www.west-wind.com/JsonService.xsvc", lcJson)

*** Alternately you can also explicitly use `.AddPostKey()` (same behavior)
loHttp.AddPostKey(lcJson)
lcJsonResult = loHttp.Post("http://www.west-wind.com/JsonService.xsvc")

* do something with the JSON data
```

The wwHttp class provides HTTP access via WinInet to Visual FoxPro application. This class provides tight control over most aspects of the HTTP protocol including HTTP headers (client and server side), timeouts, proxy support, authentication, SSL connectivity as well as providing support for asynchronous HTTP operation.

For a variety of examples of using this class please see:

* [Access HTTP content over the Web](VFPS://Topic/_S9001ZXIH) 


**Based on:** Relation  
**Stored in:** wwHTTP.prg