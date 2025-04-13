Web Connection is a complete Web application framework for developing scalable Web applications with Visual FoxPro.  It is a proven development platform with nearly a 30 year history that has proven itself stable for a wide variety of Web and Service based applications from small to large scale.

**Web Connection lets you use FoxPro code to respond to Web requests from a Windows based Web server**, using the same, familiar FoxPro language and development environment that you've always used to build other types of applications. You can use the FoxPro IDE to develop your apps, use the FoxPro debugger to step through code and use just about any non-UI FoxPro code as part of your Web applications. You can then deploy your application as an executable to a live server - typically running IIS.

To build Web applications, Web Connection provides the following:

* Low level Web Server Connector Interface to IIS, IIS Express and Apache
* FoxPro Server implementation that captures Web requests
* FoxPro abstractions to capture Web request data and produce HTTP output
* Easy abstractions: Request, Response, Process, Server, Session objects
* Simple routing of Web requests to markup pages or methods in your classes 
* HTML Scripting using FoxPro code using MVC style templating 
* REST API Services using a simple class based method call interface

Additionally Web Connection provides Lifetime and Administration features:

* Run your FoxPro server code in Visual FoxPro and use the tools you know
* Live Reload lets you see code changes in HTML and FoxPro immediately
* Support for local Web Servers so you don't have to run IIS locally
* Tools to build and bundle your applications
* Tools to help deploying your FoxPro Web applications

> #### @icon-info-circle All FoxPro - All The Time
> Other than the FoxPro to IIS interface connector, all of Web Connection's code runs inside of FoxPro. The Web Connection framework is **100% written in FoxPro and comes with source code**. Web Connection is not a black box and if registered you have all the source code to review, and if necessary, customize the framework.

## Web Server Integration
Web Connection ships with a number of connectors that interface with various Windows Web Servers. The primary interface is for IIS and IIS Express, but Apache and custom .NET Core based Web Connection Web server are also supported. These connectors are low level interfaces implemented at the Web Server that capture request information and forward it to the Web Connection FoxPro Server in a consistent matter so that you can build applications that are independent of the Web Server used.

## Web Connection FoxPro Server
The core framework provides a `wwServer` base class that you inherit from. This server knows how to communicate with the low level connector interface and is called whenever a request comes in. You can run multiple simultaneous instances of the servers you implement to handle simultaneous requests.

The core Web Connection framework provides a base level of abstraction that handles routing of requests to your custom classes, which you implement for HTTP endpoints. In essence you create a method in a class for each HTTP end point your want to handle. Web Connection provides you with intrinsic objects like `Request`, `Response`, `Server`, `Session` and `Process` that you can use to capture input information, server status and state, and generate output to send back to the server. The core engine supports request information retrieval and raw HTTP output for base functinoality.

Web Connection then provides a number of different frameworks that sit on top of the core engine to make it easy to generate HTML, JSON and binary output using more high level tools.

### HTML Generation
Web Connection provides you with lots of choices for generating output from your application. 

#### Code Level Html
At the lowest level you can do everything in code using just the core features. You can respond to requests, pick up request data with pure code and run your own FoxPro code, and then generate HTML output *by hand* using the raw Response object and the `Response.Write()` for example. 

Generating HTML output is as simple using:

```foxpro
FUNCTION HelloWorld
lcId = Request.QueryString("ID")    && id from the URL
lcName = Request.Form("Name")       && Form variable from POST data

Response.Write("<h1>Hello " + lcName + "!</h1>")
ENDFUNC
```

from a method. 

You can manually build up a string to send or use many `Response.Write()` commands, or use `TEXT TO lcHtml TEXTMERGE` to create simplistic in code templates for example. Hand coding can be useful for very simple HTML scenarios or Micro-Service applications, but for most common business scenarios you'll likely want to use external HTML Templates or a REST service to handle input and output parsing.

While that works for HTML output, it's usually **not the best approach except for very simple things** as HTML in code is too difficult to manage over time. You'll want to separate your code and your HTML templates and use dedicated HTML editors to handle creating your HTML output.

The raw approach works better for service output that produces things like JSON, XML or binary content, but even then there better ways.

Let's look at the higher level abstractions for HTML and REST Service generation.

#### Html Output with  Model, View, Controller (MVC) with Scripts and Templates
Web Connection supports MVC (Model, View, Controller) style development using code based Controllers (Process Methods) combined with Views implemented in Scripts and Templates, which allow you to externalize the HTML output generation. Scripts and Templates allow you to HTML markup mixed with FoxPro expressions and code to produce data driven HTML output.

Here's a simple example. You can implement code based logic in a controller method inside of a Web Connection wwProcess class:

```foxpro
FUNCTION CustomerList
PRIVATE pcMessage

pcMessage = "Current time is: " + TIME()
SELECT * from Customers INTO Cursor TCustomers

*** Causes script page to be loaded and evaluated
Response.ExpandScript()  && loads CustomerList.wwd script
ENDFUNC
```

`ExpandScript()` then loads the following script page that renders the cursor (partial of relevant markup displayed):

```html
<div class="alert alert-info">
    <i class="fa fa-info-circle"></i>
    <%: pcMessage %>
</div>

<h2>Customer List</h2>
<hr />
<% 
  SELECT TCustomers
  SCAN
%>
   <div class="customer-item">
        <div class="customer-header"><%= TCustomers.Company %></div>
        <div>
            <%= DisplayMemo(Tcustomers.Address) %>
        </div>
   </div>
<% ENDSCAN %>
```

> ##### @icon-info-circle Expression Encoding Differences
> You can use `<%= %>` or `<%: %>` for embedding FoxPro expressions. `<%=` generates raw, unencoded HTML output, while `<%:` generates html-encoded, safe content that prevents cross site scripting attacks by embedding script into user captured input. 

This approach allows you to manage the result output from business objects (**Model**) (or direct data access using FoxPro Commands and cursors), with the coordinating logic FoxPro code that sets up a model for rendering (**Controller**), and then delegates the actual HTML output generation to a script or template (**View**).

MVC is a popular development paradigm that's used in many other development environments like ASP.NET, Ruby on Rails, or ExpressJs on Node and many more.

For more info see:  
[Templates and Scripts](VFPS://Topic/_4DB0VHAQW)

### REST Services
Web Connection has built-in support for creating REST based JSON services that automatically produce JSON output. Using REST services you can easily build HTTP based endpoints that serve JSON data in response to requests. The framework makes it very simple to create methods that take a single input parameter that is parsed from JSON into a FoxPro object, and lets you return a result value or cursor that is turned into JSON. Building a service endpoint is essentially as easy as creating a method that returns data. 

JSON Serialization and the JSON Service feature are also available individually so you can serialize and deserialize JSON manually, or add JSON Service like features to existing Web Connection applications.

Creating a REST service method that receives a JSON input and returns a JSON result is as simple as:

```foxpro
FUNCTION StockQuote
LPARAMETER loSymbolInformation   && JSON Object passed and parsed

loQuoteServer = CREATEOBJECT("StockServer")

*** Use the input object's values to perform
*** a task and create a result value/object
loQuote = loQuoteServer.GetQuote(loSymbolInformation.Symbol)

RETURN loQuote   && return as JSON Object
```

REST call methods can retrieve JSON inputs from:

* JSON Values  (string, number, date, boolean, blob)
* JSON Objects  
* JSON Arrays

You can create JSON output from:

* FoxPro Values (string, number, date, logical, blob)
* FoxPro Objects
* FoxPro Collections and Arrays
* FoxPro Cursors

for more info see:  
[Using the wwRestProcess Class](VFPS://Topic/_45P0T1XLS)

### PDF Output
You can generate output from FoxPro reports to PDF files using the wwPDF class. Using Ghostscript, the Microsoft PDF Print Driver, XFRX or a number of other third party PDF tools, you can generate PDF output from your FoxPro reports into PDF format from your Web Connection applications.

for more info see:
[Class wwPDF](VFPS://Topic/_S8L0R8YDM)

## Build your own Framework
Web Connection provides both high level framework features as described in the previous sections, but it can also provide the tools **to build a low level framework with your own custom behaviors**. You can intercept requests at any level, right from when a requests hits Web Connection to right when it fires off into process classes. 

It's possible to create your own framework or rendering engine that can plug into Web Connection in the same way Web Connection's MVC or REST frameworks are designed on top of the core Web Connection engine. A number of frameworks like <a href="http://foxincloud.com/" target="top">FoxInCloud</a> build on top of Web Connection's core engine to provide complex high level features on top of what Web Connection provides.

## Configuration Tools
Web Connection 6.0 and later includes a number of tools to help with server configuration and it generates a configuration script you can use to configure your Web Server. Using the new project structure in v6.0 and later, projects are self-contained and mostly XCOPY deployable. Using the configuration script 
makes it easy to deploy your server once it's ready.

### Tools, Tools, Tools
Web Connection also comes with a large general purpose library of FoxPro utilities for general purpose development. It includes libraries for Internet Protocols including HTTP, FTP, SMTP, POP3 and more. There are libraries for interfacing cleanly with .NET, serializing JSON and XML, for creating multi-threaded FoxPro components, configuration, and much, much more. 

For more info see [Utility Classes](VFPS://Topic/_S7G0UEYHE)