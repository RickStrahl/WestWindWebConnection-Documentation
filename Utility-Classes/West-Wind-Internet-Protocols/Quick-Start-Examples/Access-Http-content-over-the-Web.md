The Client Tools make it easy to access HTTP content using the [wwHTTP class](vfps://Topic/Class%20wwHTTP). It provides simple functions to retrieve and post content to and from a Web Server as well as full access to advanced HTTP features.

Web content includes anything that is accessible via the HTTP protocol. This can be HTML text, plain text, JSON or XML data, binary content like Zip files, images or PDFs. You can easily use any HTTP Verb like `GET`, `POST`, `PUT`, `DELETE` etc., access secure content, set and access HTTP headers and automatically handle authentication, redirect and GZip compression/decompression.

### Simple HTTP Content Download
The simplest requests are `GET` operations to retrieve content only. The following example captures some HTML text from a URL:

```foxpro
DO wwHttp  && load dependencies

loHttp=CREATEOBJECT("wwHTTP")

*** Issue an HTTP GET operation 
lcHTML = loHttp.Get("https://west-wind.com")

? lcHtml  && Raw HTML
```
The raw result from the request is returned as a string in the return value (or a result file via the `llDownloadFile` parameter ) and in most cases that may be all you need to know.

> **Note:** Any HTML content downloaded doesn't include dependencies like images, stylesheets, JavaScript etc. The downloaded page also doesn't 'run' JavaScript. You simply get the **raw** HTML (or other) content from the server as a string or BLOB.

### Download Non-Html and Binary Data
The content you download doesn't have to be HTML or even text. You can download non-HTML text data like JSON and XML data, but you can also download binary content like Images, Pdf or Zip files.

The following downloads and saves a Zip file.

```foxpro
DO wwHttp

loHttp=CREATEOBJECT("wwHTTP")

*** Issue an HTTP GET operation 
lcZip = loHttp.Get("https://west-wind.com/files/wwClient.zip")

lcFile = "c:\temp\wwCient.zip"
StrToFile(lcZip, lcFile)

ShellExec(lcFile)
```

For file downloads it's often useful to stream the file directly to disk using the `lcOutputFile` parameter:

```foxpro
*** Stream download directly to a file
loHttp.Get("https://west-wind.com/files/wwClient.zip", "c:\temp\wwclient.zip")
```

> If you download large files it's recommended you go directly to file to **avoid excessive memory usage**. It also lets you download files much larger than FoxPro's 16mb string limit.

### Retrieving Http Response Headers and Error Information
For proper content and result identification - especially for API calls or binary downloads - you also should check for errors and frequently look at the HTTP headers to identify the content type, and the response code of whether a request worked or not and what type of content it returned. You can use the HTTP Headers to do this.


```foxpro
lcHTML = loHttp.Get("https://markdownmonster.west-wind.com")

*** Check for errors
IF loHttp.nError != 0
   ? loHttp.cErrorMsg
   RETURN
ENDIF

*** Get HTTP Response Code
? loHttp.cResultCode   && 200, 500, 401, 404, 401.1 etc.

*** Get full Http Response Header (multiple lines)
? loHttp.cHttpHeaders

*** Retrieve any HTTP header from the result
? loHttp.GetHttpHeader("Content-Type")
```

HTTP headers for the request above look like this:

```http
HTTP/1.1 200 OK
Cache-Control: private
Content-Type: text/html; charset=utf-8
Server: Microsoft-IIS/10.0
Date: Tue, 23 Feb 2021 21:13:55 GMT
Content-Length: 39718
```

> #### @icon-info-circle Always check for Errors
> When making HTTP calls you should **always** check for errors after the call to ensure you got data. At minimum check for empty results, but you can use the `nError` and `cErrorMsg` properties to get detailed error information.

> #### @icon-info-circle HTTP Headers
> Http headers includes information about a request - like the **Content Type**, **Encoding**, **Content Length**, **Authentication Information** as well as any custom headers the server is sending. Not every request needs to look at this, but it's important for generic applications that process HTTP content, or even for binary downloads that need to determine what type of content you are dealing with (for example downloading an image or file and saving it to disk).
### Posting Form Data and sending Headers to a Server

To post data to a server you generally use either `.Post()` or `.Put()`. Semantically POST is used for adding, PUT for updating, but for most Web backends this distinction is not significant and you can use the two interchangeably. POST tends to be more common.

To post data to a server using HTML Form style form submissions with urlencoded Key/Value pairs (`application/x-www-form-urlencoded` content), you can use the `.AddPostKey()` method. Use that method to add key and value pairs to POST to the server. You can also add HttpHeaders by using `.AppendHeader()` to add any standard or custom HTTP headers to your request. 

#### Generic `Send()`
At the lowest level there's the `Send()` method which is the core method that sends all HTTP requests (also: `HttpGet()` which runs the same code).  This method requires that all properties are set via properties and methods.

To make calls for specific HTTP Verbs easier, the library provides shorthand methods specific to each verb such as `Get()`,`Post()`,`Put()`,`Delete()`. These methods automatically set the verb and provide optional parameters for data to be sent. 

> #### @icon-info-circle All HTTP Verbs are supported
> Note: Although only a few of the available and most common HTTP verbs have custom methods, you can use **any HTTP Verb** by using the `Send()` method by specifying the `cHttpVerb` explicitly (ie. `loHttp.cHttpVerb = "OPTIONS"`).

#### Posting HTML Form Data
You then call `.Post()` (or `.Put()`) to make the request:

```foxpro
loHttp=CREATEOBJECT("wwHTTP")
* loHttp.nHttpPostMode = 2   && for multi-part forms

*** Add POST form variables (url encoded by default)
loHttp.AddPostKey("FirstName","Rick")
loHttp.AddPostKey("LastName","Strahl")
loHttp.AddPostKey("Company","West Wind Technologies")

*** Optionally add headers
loHttp.AppendHeader("Authorization","Bearer c42ffadef3f3aa3d5c97da77e")
loHttp.AppendHeader("Custom-Header","Custom value")

lcHTML = loHttp.Post("https://west-wind.com/wconnect/TestPage.wwd")

ShowHTML( lcHTML )
```

This formats the HTTP POST buffer for sending the variables to the Web server which simulates an HTML Web form submission which is quite common.

The above example posts using Html Form type POST data that is provided in key value pairs. wwHttp supports 2 form data post modes via the `nHttpPostMode` property:

* 1 -  `application/x-www-form-urlencoded` - html form based encoding (default)
* 2 -  `multi-part/form-data` -  used for file uploads and forms with binary data

#### Posting raw Data like JSON or XML
For raw data posts that provide a full buffer of data as-is rather than the key/value pairs shown above, you can can use the `cContentType` property to specify the content type, then POST the raw data as a single string.

The following is sufficient for sending JSON content to the server:

```foxpro
loHttp.cContentType = "application/json"
loHttp.Post(lcUrl, lcJson)
```

> ##### @icon-warning Don't mix nHttpPostMode and cContentType
> Use either `nHttpPostMode` or `cContentType` - but don't use both! Use `nHttpPostMode` for form submissions with `AddPostKey(key,value)` and use `cContentType` to send raw buffers with `AddPostKey(lcData)` or directly in `.Post(lcUrl, lcData)` or `.Put(lcUrl, lcData)`.

### POSTING Raw Data
A common scenario is to post JSON or XML to a server for some sort of Service access. In this case you'll need to use the `POST` or `PUT` HTTP operation and set the `cContentType` to the appropriate content type like `application/json` or `text/xml` for example.

To send  XML data to a server you can use the following code:

```foxpro
loHttp = CREATEOBJECT("wwHttp")

*** Specify that we want to post raw data and a custom content type
loHttp.cContentType = "text/xml"  && Content type of the data posted

*** Explicitly specify HTTP verb optionall
* loHttp.cHttpVerb = "POST"       && (PUT/DELETE/HEAD/OPTIONS)

*** Load up the XML data any way you need
lcXML = FILETOSTR("XmlData.xml")

lcXmlResult = loHttp.Post("http://www.west-wind.com/SomeXmlService.xsvc", lcXml)
IF (loHttp.nError # 0)
   RETURN
ENDIF

ShowXml(lcXmlResult)
```

If you've been using `wwHttp` for a while you might note that this syntax of using `.Post()` is new as is the `lcData` parameter. The old syntax that uses `.AddPostKey(lcData)` also still works:

```foxpro
loHttp.AddPostKey(lcXml)
loHttp.Post(lcUrl)
```

with the same behavior as the example above. The new `.Post()` syntax is just simpler and more common for HTTP requests.

#### Generic Http Requests via Send()
There are special methods for `Get(),Post(),Put(),Delete()` that map the corresponding HTTP verbs, but these are simply wrappers around the `Send()` method. If you need a custom HTTP verb, or you want more control over the configuration you can use `Send()` and explicitly set the `cHttpVerb` instead:

```foxpro
*** GET is the default
* loHttp.cHttpVerb = "GET"
lcHtml = loHttp.Send("https://west-wind.com")

*** POST
loHttp.cHttpVerb = "POST"
loHttp.cContentType = "application/json"
loHttp.AddPostKey('{ "message": "Hello World" }')
lcResult = loHttp.Send("https://somesite.com/messages/1")
```

> `Send()` replaces the ambiguously named `HttpGet()` method on `wwHttp` in version 7.2 and later. Going forward you please use `Send()`, but if you have existing code with `HttpGet()` continues to work.

#### Making a REST Service call with JSON
REST services are the latest rage, and when using REST you typically deal with JSON data instead of XML. The client tools include a [wwJsonSerializer class](VFPS://Topic/Class%20wwJsonSerializer) that can handle serialization and deserialization for you.

```foxpro
DO wwHttp
DO wwJsonSerializer

loSer = CREATEOBJECT("wwJsonSerializer")
*** Or just generate the JSON as a string
lcJsonIn = loSer.Serialize(loInputData)

loHttp = CREATEOBJECT("wwHttp")
loHttp.cContentType = "application/json; charset=utf-8"

lcJsonResult = loHttp.Post(lcUrl, STRCONV(lcJsonIn,9))  && UTF-8 Encoded

loResultObject = loSer.DeserializeJson(lcJsonResult)

*** Do something with the result object
? loResultObject.status
? loResultObject.Data.SummaryValue
```

To make things even easier with JSON REST Services take a look at the [JsonServiceClient](VFPS://Topic/_4JF1F19ZR) class which handles all the HTTP calls **and** serialization, UTF-8 encoding and decoding, and error handling all via single `CallService()` method.

The following sends JSON data to a service and receives a JSON result back as a FoxPro object:

```foxpro
loProxy = CREATEOBJECT("wwJsonServiceClient")

lcUrl = "http://albumviewer.west-wind.com/api/album"
lvData = loAlbum && FoxPro object
lcVerb = "PUT"   && HTTP Verb

*** Make the service call and returns an Album object
loAlbum2 = loProxy.CallService(lcUrl, lvData, lcVerb)

IF (loProxy.lError)
   ? loProxy.cErrorMsg
   RETURN
ENDIF   

? loAlbum2.Title
? loAlbum2.Artist.ArtistName
```

### Sending Raw Binary Data
You can also send binary data, which in FoxPro is just represented as a string. Make sure you set the `.cContentType` property to specify what you are sending to the server:

```foxpro
loHttp = CREATEOBJECT("wwHttp")

*** Specify that we want to post raw data with a custom content type
loHttp.cContentType = "application/pdf" 

lcPdf = FILETOSTR("Invoice.pdf")
loHttp.AddPostKey(lcPdf)  && Add as raw string

*** Send to server and retrieve result
lcHtml = loHttp.Post("http://www.west-wind.com/SomeUril.xsvc")
```

Note that you can use the cContentType property to specify the content type of the data POSTed to the server. 

### Send a File As an HTTP Form Upload (multi-part forms)
Many APIs and applications may also require form based file uploads which uses a special HTTP format (known as multi-part encoding) to upload files via HTTP. This format requires that you use `nHttpPostMode=2` and you can then use `AddPostFile()` and `AddPostKey()` to add form variables to the POST operation:

```foxpro
loHTTP = CREATEOBJECT("wwHTTP")

*** Posting a multi-part form with a File Upload
loHTTP.nHttpPostMode = 2  && Multipart form encoding

*** Post a file
loHttp.AddPostFile("File","d:\temp\wws_invoice.pdf",.T.)

*** Post normal text (part of the same form)
loHttp.AddPostKey("txtFileNotes","test note")

lcHTML = loHTTP.Post("http://localhost/wconnect/FileUpload.wwd")
```

### Streaming Downloads directly to file
You can also stream the content from a URL directly to a file which is useful to avoid FoxPro's 16mb string limit and also because it's faster and less resource intensive. 

To do this you can use additional parameters on the `.Send()`, `.Get()`, `.Post()`, `.Put()` and `.Delete()` methods which take an `lcOutputFilename` parameter to allow streaming the HTTP data into. This allows for large files to be downloaded without tying up memory as they normally would.

```foxpro
loHttp = CREATEOBJECT('wwhttp')
loHttp.Get("http://www.west-wind.com/","c:\test.htm")

IF loHttp.nError # 0
	wait window loHttp.cErrorMsg
	return
ENDIF

*** Browse the file from local disk
GoUrl("c:\test.htm")
```

Streaming to file allows you to bypass large memory usage as `wwHTTP` directly streams into the file with small chunks read from the server. This allows files much larger than 16mb to be downloaded.

### Reveiving Download Event Notifications
`wwHttp` also includes an **OnHttpBufferUpdate event** wich provides you download progress information via an event handler on the wwHttp class. You can find out more on how this works and an example here:

[wwHttp::OnHttpBufferUpdate](vfps://Topic/_0JJ1AFUB8)