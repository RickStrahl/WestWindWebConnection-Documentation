Up to this point we've only discussed the server object, whose job it is to grab a Web request from the Web server and pass it on to you. The `wwProcess` subclasses you create are the heart of your Web Connection applications as these classes represent your entry point for each of your requests.

`wwProcess` works by getting called from the wwServer class and executing a method in your server based on the URL passed. The URL contains information regarding which method to call.

To review the server does the following:

* Request hits the `wwServer::ProcessHit()` method
* A `wwRequest` object is created
* The server invokes your `wwServer::Process()` method
* Process contains the `CASE` statement logic to route your request to the class of your choice.
* Your Process class stub code is started. The stub loads your custom wwProcess subclass.
* The `wwProcess::Process()` method is called. 
* As part of `Process()` the `OnProcessInit()` method is called where you can hook 'global' startup code
* `Process()` reads the URL to figure out which method to call in the object.
* Your custom method is called - you're now running your user FoxPro code. Request and Response objects are available to retrieve input and write output into the HTTP stream.
* You exit from your method code and the framework routes the Response output back to the Web server.


### Creating your wwProcess Subclass
So, how do we create this wwProcess class?

The easiest way is to use the Wizards. Both the new Project Wizard and the New Process Wizard create wwProcess subclasses that manage the entire process for you.

Behind the scenes, the wwProcess implementation works like this:

* You create a PRG file that contains your wwProcess subclass and a small stub program that loads the class.
* wwServer calls the PRG file with a reference to itself as a parameter.
* The stub creates an instance of your wwProcess class and passes the server reference which contains a Request object created by the server.
* Based on the URL the wwProcess::Process method handles routing the request to a specific method in your server. The URL either uses positional (wc.dll?wwDemo~TestPage) or scriptmapped (TestPage.wwd) syntax to route the request.
* Your method gets control. Request and Response objects are available to receive inputs and send output.
* When you're done simply return from your method.

### Looking at a wwProcess subclass
The wwProcess class is mostly a container for other objects in order to simplify access to the other objects throughout the framework. As such only a few custom methods and properties are defined in this class.  The object references are used to create a new Process object which builds references to them and uses them internally making them available for your own routines. The setup code for the UDF and class creation looks like this:

```foxpro
* PROCEDURE wwDemo
LPARAMETER loServer
#INCLUDE WCONNECT.H

loProcess=CREATE("wwDemo",loServer
loProcess.Process()

RETURN
```

The code above basically receives the two objects as parameters then creates a Process object and calls its Process() method which is its entry point for Web request processing. 

The actual wwProcess class implementation needs to be subclassed by you in order to attach custom processing methods to the code. The minimal wwProcess subclass looks like this:

```foxpro
*************************************************************
DEFINE CLASS wwDemo AS WWC_PROCESS
*************************************************************

************************************************************************
* wwDemo :: HelloWorld
**********************
*  URL to arrive here: wc.dll?MyPrg~HelloWorld
*   or with a scriptmap:  Helloworld.myScript
************************************************************************
FUNCTION HelloWorld

THIS.StandardPage("Hello World","Hello from Visual FoxPro. "+;
                                "The current time is: <b>"+Time()+"</b>")

RETURN
*EOF TestPage

FUNCTION OtherRequest

Response.HTMLHeader("Another Request")

For x = 1 to 10 
   Response.Write("line " + TRANS(x) )
ENDFOR

ENDFUNC

ENDDEFINE
```

WWC_PROCESS is a constant defined in wconnect.h that defaults to wwProcess. This #define allows you to override a common subclass used for all of your Process subclasses.

Each additional request you create with a link from an HTML page (link or Form button that calls wc.dll) needs to have a corresponding method in this class. Your subclass of wwProcess can contain as many methods as you see fit, although it's best to logically break up the size of classes in separate program files or classes. In order to do so you'd pass a different first 'parameter' on the URL (in the example above MyPrg - MySecondPrg for example) to route to another PRG file that can handle another set of requests using another subclass of the wwProcess object. Remember each additional PRG file will need an entry in the wwYourServer::Process() method's CASE statement. The parameter can either be in the form the first URL parameter in a ~ separated list or preferrably a custom script map for your process (Helloworld.myScript).

### Code to fire on every request to your Process class
Often times you'll want to have some code fire on every request to your page. Common things are initializing the Session object or authenticating users on every request. Instead of adding the code to do this to every request or a large number of selected requests you can use the OnProcessInit method which fires before a request is fired against the actual implementation method.

Here's an example that initializes the Session object and checks all requests against an Admin directory to authenticate:

```foxpro
************************************************************************
* webConnectDemo :: OnProcessInit
*********************************
FUNCTION OnProcessInit

*** Use Session in this application
THIS.InitSession("wwDemo")

*** Check for user login - no login/no access
IF ATC("/admin/",Request.GetLogicalPath()) > 0
   THIS.cAuthenticationMode = "UserSecurity"
   IF !THIS.Authenticate()
      RETURN .F.
   ENDIF

   * THIS.cAuthenticationMode = "Basic"
   * IF !THIS.Authenticate("ANY")
   *    RETURN .F.
   * ENDIF
ENDIF

RETURN .T.
ENDFUNC
```

### Error Handling
How errors are handled in the Process class depends on the Server.lDebugMode flag. This flag determines whether errors are handled or cause VFP to stop in the debugger if an error occurs. During development you'll generally will want the lDebugMode flag to .T. so you indeed stop on the code where an error occurs. For deployment you'll want to set Server.lDebugMode to .F. so that errors are handled and display an error page instead.

The Server.lDebugMode flag can be set in the YourApplication.INI file as DebugMode, or on the Server Status form using the DebugMode Checkbox.

Errors are handled by the base Process() method through a TRY/CATCH construct which is conditionally enabled/disabled based on the Server.lDebugMode flag. When an error is trapped by the TRY/CATCH it fires the OnError() method on your Process class with an Exception object as a parameter. You can override this method to provide custom error behavior.

The stock behavior does the following:
* Display an error page with error information
* Writes the error to the Request Log with Error flag set to .T.
* Emails the error information to the Administrator Email if configured.

Here's what the stock error handler looks like:

```foxpro
************************************************************************
* wwProcess :: OnError
****************************************
***  Function: Called when an error occurs and Server.lDebugMode is off.
***    Assume:
***      Pass: loException
***    Return: .T. if you completely handled the error including output
***            generation. Else return .F.
************************************************************************
FUNCTION OnError(loException as Exception)


*** Shut down this request with an error page - SAFE MESSAGE (doesn't rely on any objects)
THIS.ErrorMsg("Application Error",;
   "An application error occurred while processing the current page. We apologize for the inconvenience. " + ;
   "The error has been logged and forwarded to the site administrator and we are working on fixing this problem as soon as we can.<p>" + ;
   "<p align='center'><table cellpadding='5' border='0' background='whitesmoke' width='550' " +;
   "style='font-size:10pt;border-collapse:collapse;border-width:2px;border-style:solid;border-color:navy'>" + CRLF +;
   "<tr><td colspan='2' class='gridheader' align='left' style='font-weight:bold;color:cornsilk;background:navy'>Error Information:</th></tr>" + CRLF +;
   "<tr><td align='right' width='150'>Error Message:</td><td>"+ loException.Message + "</td></tr>" + CRLF +;
   "<tr><td align='right'>Error Number:</td><td> " + TRANSFORM(loException.ErrorNo) + "</td></tr>" +  CRLF +;
   "<tr><td align='right'>Running Method:</td><td> " + loException.Procedure + "</td></tr>"+CRLF+;
   "<tr><td align='right'>Current Code:</td><td> "+ loException.LineContents + "</td></tr>"+CRLF+;
   "<tr><td align='right'>Current Code Line:</td><td> " + TRANSFORM( loException.LineNo) + "</td></tr>"+ crlf +;
   "<tr><td align='right'>Exception Handled by:</td><td>" + THIS.CLASS + ".OnError()</td></tr></table></p>")

*** Log the Request
IF TYPE("THIS.oServer")="O" AND !ISNULL(THIS.oServer)
   this.LogError(loException)
ENDIF

*** We've completely handled the error!
RETURN .T.
ENDFUNC
*  wwProcess :: OnError
```

The call to LogError() logs and also emails if email configuration is setup. Note the default implementation uses the wwProcess::ErrorMsg() function to display the error message on an HTML page. As you can see the method is simple and easy to override to provide your own error customization as are the ErrorMsg() method which provides the stock display format for error messages.

### Using the Request and Response objects
When your class method fires you will have available to you the Request and Response objects which Web Connection creates for you as part of its default wwProcess::Process() method. Since that method calls into your method these PRIVATE scoped objects are in scope everywhere in your request processing code and you can access them directly.

The example above uses the Response object to Write() a few values to the HTTP output stream. Mostly this will be HTML, but it can be anything. 

Along the same lines you can use the Request object to retrieve input from the user and the environment. Request.Form() lets you retrieve HTML form variables, Request.QueryString() lets you retrieve parameters and Request.ServerVariables() lets you retrieve any of the system variables the server makes available. Request also has many custom methods that are wrapped around ServerVariables to retrieve values with more memorable names than the Server Variables.


For a more hands on example on how this all works see the [Step by Step Guide](vfps://Topic/Step%20by%20Step%20Guide).