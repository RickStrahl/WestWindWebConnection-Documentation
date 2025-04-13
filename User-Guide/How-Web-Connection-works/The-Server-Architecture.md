Web Connection is made of two separate components that comprise the full Web Connection framework:

* An Web Server Connector Component (.NET or ISAPI)
* A FoxPro Web Connection Server Instance

![](//images/Howitworks/ServerArchitecture.jpg)

### The .NET or ISAPI Web Connector 
The Web Connector component is a native Web Server component implemented in .NET (or ISAPI) that intercepts all requests routed to your Web Connection application and captures all the request information, which is then packaged and passed to the FoxPro Web Connection Server instance. 

When a request first comes in the Web Connection connector is loaded. Depending on which mode the server is running in it may also pre-load all configured Web Connection FoxPro server instances to which requests are later routed.

The connector intercepts each request that is routed to it which is usually determined by a **script map**. Script maps are file extensions and the Web Connection Module is usually mapped to a specific script extension in `web.config` that map an extension to the specific handler.

Anytime a URL for this application is accessed in the browser using one of these extensions it fires the Web Connection connector which then forwards the request to your Web Connection FoxPro server instance waiting for requests.

### The FoxPro Web Connection Server
The FoxPro server is a standalone FoxPro application that can execute either as an EXE launched from Explorer, or as a EXE COM (DCOM) server. You build this EXE the same way you build any other FoxPro application, but the main component of this EXE is a server class - [wwServer](https://webconnection.west-wind.com/docs/_s7c0oeq2b.htm) - that handles the interaction with the Web Server. Your application implements a subclass of `wwServer` that customizes the server for your specific application.

When the connector picks up a request it fires off the request to the FoxPro server which picks up the request. It parses the incoming request data into a [wwRequest](VFPS://Topic/_S850QFR2E) object which is then used to parse out the URL and any other data that was passed from the user and the Web Server.

The first thing that happens is the URL is parsed and routed to an appropriate handler method in a `wwProcess` subclass that you create as part of your application. This method is where user code handles an incoming request by using the `Request` object to retrieve input data from the request (form data, query string values, server variables, Session data etc.) and writes output using the [Response](VFPS://Topic/_1O80YQ37Z) object which writes output into the HTTP output stream. The `Response` object provides low level output methods to write raw data into the HTTP output stream, but there are also higher level frameworks like the **MVC Scripting Framework** that provide a more flexible HTML template driven approach to create your output in an easier to manage way.

Regardless of low level features, or framework output is always written to the `Response` object eventually which is captured by the server. Note also that output doesn't have to be HTML - you can also return binary data like a PDF document, or text data like JSON or XML or a binary zip file.

When the Process class processing is complete, the Server class picks up the completed HTTP output and sends it back to the Web Connection connector in the Web Server, which picks it up and writes the output into the Web Server's output pipeline for final display in the browser for the user.

### File and COM Modes
The Visual FoxPro server applications can run in a couple of ways:

* COM Mode - for deployed Applications
* File Mode - for Interactive Development

**COM Mode** is the preferred way to execute a **live production application** because it is faster, provides a managed thread pool for running and managing multiple COM instances and provides additional management features for starting and stopping and otherwise controlling server instances.

**File mode** is meant **for development** and uses semaphore message files to communicate and pass data back and forth between the Web Server and your FoxPro application. Because COM is difficult to debug during development, using file based mode makes it possible to run and debug Web Connection applications in the Visual FoxPro IDE like any other FoxPro application which greatly simplifies the development process. 

Both COM and File based messaging use the same message protocol so they are almost completely interchangeable using a configuration switch. Switching between File and COM Modes is mostly transparent - both operate in identical manners once a request is received.

### Processing Requests
When a Web request comes in it is routed to a Web Connection server instance. The server is built using pure Visual FoxPro code and it contains the Web Connection Framework plus your application specific implementation code that handles incoming requests.

The processing sequence of events inside of Web Connection is:

```text
wwServer::Process
  wwProcess::RouteRequest()
     YourClass::Method()
```     



The server picks up a request and parses out the URL to determine which handler to call. A *handler* is a FoxPro class and a method within that class. The server also parses out all the incoming request data and makes it available to your code via a `Request` object that lets you access query string data from the URL, form data on POSTed forms, information about the URL, the IP address, whether the request is secure and so on. The `Request` object becomes the input to your application.
  
The default routing mechanism routes each request to a method in a `wwProcess` subclass that you create (or is created for you using the New Project/Process Wizards). 

A typical URL looks like this:

```
http://localhost/wconnect/Testpage.wcs?id=10
```

This basically says:

* Route to the `wconnect` application
* Instantiate the `wwDemo` class (the .wcs extension)
* Execute the `TestPage()` method
* Pass a an ID with value of 10 on the query string

This routes the request to the `TestPage()` method, which then uses the `Request` object to provide input values, and can use the `Response` object to write output into the HTTP stream.

The following uses the low level Response functions of generating HTTP output using only code:

```foxpro
FUNCTION TestPage
LOCAL lcId

*** Pick up the ID from the querystring
lcId = Request.QueryString("id")

Response.Write(this.PageHeaderTemplate("Hello World from Visual FoxPro"))

Response.Write("Welcome to Web Connection. The time is: <b>" + ;
               TRANS(DateTime()) +;
               "</b>")

Response.Write("<hr/>")
Response.Write("Id passed on query string:" + lcId )


Response.Write(this.PageFooterTemplate())

RETURN
```

The output you generate is entirely up to you and you can use any FoxPro feature available except for UI functions (forms, controls etc). The code's job is to generate HTML output here and dump it into the `Respones` object via the low level `Response.Write()` method.

The code above uses the low level `Response.Write()` method plus a few useful helper functions of the Process class that inject an HTML document header and footer based on templates stored in the `~\Views` folder of the Web site. But those things are optional.

### Using MVC Style Scripting
The preferred approach to create HTML content is to use templates or scripts rather than generating HTML in code. This approach is generally known as MVC (Model, View, Controller) and makes it much more maintainable to create HTML you can edit in a proper HTML editor like [Visual Studio Code](https://code.visualstudio.com/). 

You can use built-in **MVC Scripting Framework** to create  HTML templates that can merge HTML and FoxPro code using expressions or code blocks. 

Using this mechanism you can create HTML pages that contain the HTML content with embedded expressions and code blocks. These pages can either be self-contained (not recommended) as standalone pages in the Web site, or be combined with a method in a class that provides the base processing to create a model that is then displayed in the view.

This is generally know as Model, View,  Controller (MVC) in which the FoxPro method is the Controller, the Model is your data which can be one or more objects and/or open cursors that become visible in the View, which is the HTML template.

Here's an example of using MVC style scripting with a Process method to drive a `Helloworld.wcs` request:

```foxpro
*********************************************************************
FUNCTION HelloScript()
************************
PRIVATE poError 

*** Configure a model and pass it into the script
*** Use this to display an ErrorDisplay object
poError = CREATEOBJECT("HtmlErrorDisplayConfig")
poError.Message = "Welcome from the " + this.Class + " class, using MVC scripting."
poError.Icon = "info"

*** Run a query and pass the data to the script
SELECT TOP 10 * FROM wwRequestLog  ;
INTO CURSOR TRequests ;
ORDER BY Time Desc

*** Render HelloScript page template
Response.ExpandScript()

USE IN TRequests
ENDFUNC
* EOF HelloScript
```

This controller is passing a `poError` object and the `TRequests` cursor to the script for processing. The script then uses the object and the cursor to render the HTML output using FoxPro `<%= expression %>` or `<% codeBlock %>` to merge the data into the code.

```html
<% 	
	pcPageTitle = "Hello Script World" 	
%>
<!-- using a master layout page that provides the page chrome -->
<% Layout="~/views/_layoutpage.wcs" %>

<div class="container">
    <div class="page-header-text">
        <i class="fas fa-globe-americas"></i>
        Hello Script World
    </div>


    <!-- poError is set in the HelloScripting Method and passed as a private variable -->
    <%= HtmlErrorDisplay(poError) %>

    <p>
        Hello from <%= Version() %>.
        <br />
        The Time is: <b><%= ShortDate( DateTime(),2) %></b>.
    </p>

    <h3>10 Most Recent Requests</h3>
    <table class="table table-striped table-dark-headers table-bordered">
        <thead>
            <tr>
                <th>Time</th>
                <th>Request</th>
                <th>Verb</th>
                <th>IP Address</th>
            </tr>
        </thead>
        <tbody>
            <%
            SELECT TRequests 
            SCAN
            %>
            <tr>
                <td><%= ShortDate(Time,2) %></td>
                <td><%= Script + IIF(!EMPTY(QueryStr),"?" + QueryStr,"") %></td>
                <td><%= Verb %></td>
                <td><%= RemoteAddr %></td>
            </tr>
            <% ENDSCAN %>
        </tbody>
    </table>
</div>            
```

You can see in this example that a number of FoxPro expressions are evaluated from the object, from FoxPro built-in functions and from the cursor.

The output generated looks like this:

![](//images/Howitworks/ScriptingPage.png)

Note that this page displays a lot more content - the page header and footer - that the template does not directly reference. Rather, the template references a `Layout` page in the header that provides a base layout into which the page above is rendered, which lets you build smaller pages with just the content that's page specific, without having to recreate or copy the same page layout markup into every page.

You can learn more about scripts, layout pages, partials and more here:

[Templates Scripts - MVC for Web Connection](https://webconnection.west-wind.com/docs/_4db0vhaqw.htm)

### Other Output

The code that fires is responsible for generating HTTP output for an incoming request. In most cases this output will be HTML, but it can also be XML, or binary content such as a data file, a Word or PDF document for example. The result is returned through the Web Connection Framework - typically the Response object - and returns essentially a string that is then displayed in the browser or served to an HTTP client.

### NOT USED 

This causes the Web server to load the ISAPI extension into memory - once it's loaded it stays loaded and the single instance of the DLL is recycled. The ISAPI extension is written in C++ and is a true multi-threaded ISAPI extension, which means it can take multiple requests simultaneously on multiple processor threads.

The ISAPI DLL receives the incoming request and picks up all of the server's request information and encodes it into string and passes this information forward to the Visual FoxPro Web Connection server. The server picks up the request information and uses this information as the request input much like you would fields on a form or parameters. 

At this point your server code has full control and can run any Visual FoxPro code using the Request object to retrieve input and the Response object to send output into the HTTP output stream. You can use FoxPro tables and cursors, you can use SQL Server if you like, you can call COM objects - just about anything that the FoxPro language allows except user interface operations which for obvious reasons won't work in a Web environment.

The mechanism and syntax for the actual request processing varies depending on the method you use to generate your output. You can use low level code that uses the Response object directly and allows raw access to the output generated directly, or you can use high level tools like the Web Control Framework that abstract the output processing using an object oriented and control based model. Other tools allow generation of PDF documents from reports, parsing Visual FoxPro forms to HTML and to provide various Web Service type interfaces.

In all cases the output is channelled through the Response object and is then passed back to the ISAPI extension, which in turn sends the output back to the Web server.

The mechanism used to pass data between the ISAPI connector and the Visual FoxPro server depends on the [messaging mechanism](vfps://Topic/Messaging%20Modes%20-%20File%20Based%2C%20COM%20and%20ASP) used: File based, COM based or ASP.