The Web Connection Framework classes are responsible for passing data between the Web Server and your application code. The base classes consist of the `Server`, `Process`, `Request` and `Response` objects which are all exposed as `PRIVATE` variables in your Process method/Controller code. The `wwServer` and `wwProcess` classes control application flow and mechanics. `wwRequest` and `wwResponse` classes expose incoming Web request data and control output generation for the HTTP response you create on each request.

![](IMAGES\FRAMEWORKCLASS.JPG) 

## wwServer
The wwServer class is the entry point into a Web Connection application. Your application implements a subclass of wwServer and any incoming requests are then routed to the `wwServer::Process()` method which in turn routes to a `wwProcess` class for actual request processing. A subclass of the server typically deals with initialization and configuration of the server in the `OnInit()` and `OnLoad()` methods.

#### Low Level Plumbing and Routing
wwServer provides the low level plumbing and logistics for handling incoming Web server requests. The class receives inbound requests into the `ProcessHit()` method, which then forwards to the `Process()` method that is overridden in every wwServer subclass. `Process()` then looks at the incoming URL and based on the URL query string or script extension routes the request to the appropriate `wwProcess` subclass of your application. A single server can route to multiple `wwProcess` classes which are differentiated based on the URL or script map extension. 

What this means is that requests like:

```
SomePage.wwd
SomePage.wws
```
are routed to different Process classes and methods based on the different extensions. So `SomePage.wwd` might get routed to `wwDemo::SomePage()` and `SomePage.wws` to `wwStore::SomePage()`.

wwServer also is responsible for picking up incoming request information and creating a `wwRequest` and `wwResponse` instance so that your application code has easy access to the inbound request data and HTTP output stream. 

To create a new Web Connection Server, the easiest way is to use the [New Project Wizard](vfps://Topic/New%20Project%20Wizard) to set up a new Web Connection Application.

## wwProcess
The wwProcess class is the main request handler for a Web Connection application. Each request that is fired into Web Connection from the Web Server gets routed by the `wwServer` class into a `wwProcess` instance. wwProcess classes implement individual methods and each method corresponds to an HTTP endpoint handler that serves a particular URL. To implement a wwProcess handler or 'Controller' method you simply implement a new method on your wwProcess subclass.

wwProcess Controller methods typically do two major things:

* Read input data or Information from the wwRequest object
* Perform some data or operational tasks based on that incoming data
* Create an HTTP output using the wwResponse object

A very basic Process Controller method looks like this:

```foxpro
FUNCTION ShowCustomer

*** Capture input from wwRequest
lcId = Request.QueryString("id")

*** Perform some business logic
loBus = CREATEOBJECT("cCustomer")
loBus.Load(lcId)
loCust = loBus.oData

TEXT TO lcHTML NOSHOW
<html>
<body>
    <h1><< loCust.Company >></h1>
    <hr />
    <div><< loCust.Firstname >> <<loCust.LastName >></div>
    <div><< DisplayMemo(loCust.Address) >></div>
</body>
</html>
ENDTEXT

*** Generate output into the HTTP response with wwResponse
Response.Write(lcHtml)
ENDFUNC
```

Note that wwResponse has many mechanisms for creating HTTP/HTML such as using `Response.ExpandScript()/ExpandTemplate()` for externally stored HTML markup. The code above demonstrates the lowest level `Response.Write()` to keep things self-contained and simple for this example.

Every new Web Connection application created includes a default wwProcess implementation and you create it using the [New Project Wizard](vfps://Topic/New%20Project%20Wizard). You can also add a new wwProcess class to an existing Web Connection application using the [New Process Class Wizard](VFPS://Topic/_S7R13T6G7).

## wwRequest
The wwRequest class is responsible provides access to incoming Web Server request data. This includes HTML form data, URL Querystring data, Authentication Info, client info such as client IP Address and the Browser user agent, and  Web Server information such as the server name and application paths and much more. You can use this information to read input from the Web page, the URL and the server's status.

wwRequest serves as your request input. 

Common methods you use are the `.Form()`, `.QueryString()`, `.ServerVariables()`, `.GetAuthenticatedUser()`, `.GetIpAddress()`, `.GetBrowser()` and so on. For more info check the [wwRequest documentation](VFPS://Topic/_S850QFR2E).

wwRequest is created for you as part of the initial wwServer request processing and is available as the `PRIVATE` variable named  `Request` in all process methods, or available on the server as `Server.oRequest`.

This object is automatically created for you in the wwProcess class and is always available as THIS.oRequest or simply `Request` in any method of  your wwProcess subclass.

## wwResponse
The wwResponse class handles all output generation in Web Connection. 

At the lowest level the `Response.Write()` method sends output into the HTTP output stream that gets sent back to the Web server. Web Connection provides a number of additional features on the Response object, such as manipulating headers with methods such as `AppendHeader()`, `AddCookie()`, `Authentication()` and higher level functions for generating output like `ExpandTemplate()` and `ExpandScript()`, `DownloadFile()`.

This object is automatically created for you in the wwProcess class and is always available as THIS.oResponse or simply `Response` in any method of  your wwProcess subclass.

### It's Easier than it looks
While it sounds like there's a lot happening to get requests routed and fired, you as a developer don't have to worry about the details. 

As a developer all you need to really do is:

* Create a wwProcess class
* Implement one method for each HTTP endpoint
* Use the Request object to read inbound data
* Run your business logic
* Use the Response object to write outbound data

This is really the core of what every single request in Web Connection is all about. It's very easy to get started.

{{ Helpers.ChildTopicsList("classheader") }}