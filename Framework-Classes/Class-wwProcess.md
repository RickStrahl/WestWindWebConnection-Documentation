***Handling requests and serving as the developer's entry point for Web requests. Most of your Web interface coding will occur at this class level.***

The wwProcess class is the heart of Web Connection from the developer's point of view. This class serves as your code entry point where custom logic occurs. Each request in your Web application maps to a method in a wwProcess subclass. To add new functionality you simply add a method to your implementation of the process class. The simplest implementation looks like this:

```foxpro
*************************************************************
DEFINE CLASS YourProcess AS WWC_PROCESS
*************************************************************

*********************************************************************
FUNCTION HelloWorld()
*********************

THIS.StandardPage("Hello World from the " + THIS.Class + "  process",;
                  "If you got here, everything should be working fine. The time is: " + TIME())
                  
ENDFUNC
* EOF HelloWorld

*********************************************************************
FUNCTION QuickData()
********************

IF !USED('tt_cust')
   USE (THIS.cAppStartPath + "tt_cust")
ENDIF

Response.HTMLHeader("Customer List")
Response.Write("Filename: " + DBF() )
Response.ShowCursor()
Response.HTMLFooter()

ENDFUNC

ENDDEFINE
```

which can be accessed with a URL like this:

**Full Url Syntax**
```
wc.wc?YourProcess~HelloWorld
```

**Script Map syntax**(where wc.dll is mapped to .YP)

```
HelloWorld.yp
```

Each additional method you add can be referenced in the same manner. Note that in the above StandardPage() is quick method that creates a fully self contained HTML page. For a more customized page you can use the wwResponse object (THIS.oResponse.Write() for example) to create programmatic output or expand template/script pages using the ExpandTemplate/ExpandScript methods of the wwResponse object.

The easiest way to create a new process class is to use the New Process Wizard from the [Web Connection Management Console](vfps://Topic/The%20Web%20Connection%20Management%20Console), which hooks up new process entries to the mainline and creates a skeleton class that you can simply start adding methods to.

<H3>Exposed generic objects</H3>
wwProcess exposes several objects as PRIVATE variables for easier reference:

* **Request**  
This object is mapped to wwProcess::oRequest.

* **Response**  
This object is mapped to wwProcess::oResponse

* **Server**  
This object is mapped to wwProcess::oServer

* **Process**  
The wwProcess object that is running the current request. In code this would be referenced as THIS, but in script and templates that value is not valid - use Process instead.

* **Session**  
The wwSession object if you created one. This maps to wwProcess::oSession, and is only valid if you either call InitSession or manually create a session and assign it to this property. Otherwise the value is .NULL..

These objects are available everywhere in your applications since they sit at the top of the call stack when your code gets called. This means these objects are in scope in your request methods, as well as further down in your business objects. They are also in scope in in Script and Template files where you can do things like the following:

```
<%= Request.GetBrowser() %>
```