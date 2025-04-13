Debugging is one of the strengths of the Web Connection framework when compared to a script or pure COM based architecture because the framework allows you to operate your Web backend server inside of the VFP development environment.

### Standard VFP Debugging
The key to debugging your application is that if you're running File Based operation you can the server inside of VFP and you can simply use standard VFP Debugging features like SET STEP ON and using the VFP debugger to step through your request code.

![](IMAGES\STEPBYSTEP\DEBUGGER.GIF)

The figure above demonstrates that you can set breakpoints and step through live Web requests in the VFP debugger.

### Error Handling
Web Connection runs using a Server.lDebugMode flag that is configurable in your `yourServer.ini`. When .T. no error handling is in effect, which causes your program to simply stop on the offending line of code. During development this is a huge time saver in helping you locate and fix errors quickly at their source.

Come deployment time you can flip the Debug mode switch either in code or via the configuration option and all of Web Connection's Error handlers kick in to provide bullet proof trapping of errors so that your server doesn't crash or hang. With error handling enabled an error message is displayed (calling the wwProcess::ErrorMsg method to display the error) and logging the error into the RequestLog file. This message can be customized by overriding the ErrorMsg method for display purposes and the Error method for custom error handling.

The DEBUGMODE flag is contained in wconnect.h. It's a constant in a header file and in order to change this flag you have to recompile your server application. For more info See the [Error Handling](vfps://Topic/Request%20Error%20Handling) topic.

### Checking Web Request and Response Input and Output
An extremely useful mechanism for debugging problems that deal with incoming request data such as HTML Form Variables, Client IP, Authentication, Cookies and so on as well as checking for output issues is to use the Status Form's Save Request file option. When this checkbox is checked the last HTML result and the Request input are saved into your temp path specified in the Server setup window as TEMP.HTM and TEMP.INI respectively. You can view this output by clicking on the Display button. The pop up menu lets you view the last request as well as the last HTML output.

![](IMAGES\MANAGEMENTCONSOLE\SERVERSTATUS_SAVEREQUEST.GIF)

Use this feature to check output and make sure that you are returning exactly what you're expecting to return. One important thing to look for is the HTTP header of the resulting HTML document. The Request data file input can be very useful in seeing whether all the form variables from a form are retrieved. This lets you see exactly what the server is sending you on each request and lets you access some of the keys that are not exposed directly by the wwRequest class methods. Other things you can use the request data for is verifying that input contains expected cookies and authentication tokens which would otherwise be difficult to debug and troubleshoot.

Make sure you use this feature whenever you see inconsistent inputs in your application. The request input data comes directly from the Web server and passed straight through the ISAPI extension - if it's not there the Web server didn't send it!

Using the wwProcess::lShowRequestData flag
You can use the wwProcess::lShowRequestData flag to force the full request data to be appended to the end of a HTTP Response (actually it work correctly only with HTML content because any other content type might get corrupted by the data at the end). You can do this either at the request level by setting it in the method you're calling or at the process level.

Method level:
```foxpro
Function DoSomeThing

*** Append request data to the end of the request
THIS.lShowRequestData = .T.

Response.ExpandTemplate(Request.GetPhysicalPath())
ENDFUNC
```

Process level in OnProcessInit:

```foxpro
FUNCTION OnProcessInit()

THIS.lShowRequestData = .T.   
...

ENDFUNC
```

Note that you can optionally read the loServer.lShowRequestData flag which is loaded from the application's INI file using the ShowRequestData flag.