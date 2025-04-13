Web Connection provides several built in facilities for error handling via TRY/CATCH exception handlers. In order to efficiently debug Web Connection applications a debug mode is provided that bypasses the error handlers and allow you to stop code operation at the source of an error.

### The Server.lDebugMode flag
The wwServer class - referenced usually with Server in Process level code - exposes an lDebugMode flag that determines how errors are handled in the framework. 

The DebugMode flag can be set in the following places:
* Server.lDebugMode property
* DebugMode key in the <yourapp>.ini file
* DebugMode checkbox in the Server Status Form
Changes made to the debug mode flag are dynamic and take effect immediately.

Essentially when debug mode is .T. errors stop at the source of the error with Web Connection removing it's core error handlers from the error chain, so that you can fail at the point of error and fix the problem any way that makes sense. If lDebugMode is set to .F. Web Connection's error handlers kick in and errors are handled and routed to predefined error handlers of the framework. 

Default error handling behavior is provided and you can override this behavior. Error handlers exist in:

* **The wwProcess::OnError method**  
This is the core error handler that receives most errors that occur during request processing. The call to the wwProcess class' Process method is wrapped in a TRY/CATCH block and any error that occurs in the processing of the request is captured and fired into the OnError method which receives the Exception object as a parameter. Web Connection provides a default implementation in wwProcess::OnError, and you can override this method to provide your own error handling logic. The core handler displays an error message with error information, logs the error into the Request Log and sends an administrative email if configured (in <yourapp>.ini).

* **The wwWebPage::OnError method**  
You can also capture errors at the Page level for Web Control Framework pages. If an error occurs when Server.lDebugMode is .F. the error is routed to the OnError routine of the page with the Exception object. The TRY/CATCH is created in the Run() method of the wwWebPage class and on failure calls OnError with the Exception object as a parameter. You can return .T. or .F. from the method which indicates whether you handled the error entirely (.T.) which means no further error handling fires up the chain, or whether you didn't handle the error completely (for example, if you want to do some cleanup or custom logging etc.) in which case the original error is re-thrown and fired back up the error chain to be handled by the Process OnError method. The default behavior of this handler is to return .F. which simply fires the error up the chain.

* **wwServer::OnError method**  
This is a last resort error handler in case there's an error in your own (or ours) wwProcess::OnError routine. This handler only wraps the Process() method call and does not fire on startup failures. Server startup failures are not specifically trapped even when lDebugMode = .F. If you want to trap these errors you can create an Error method on your server and handle it from there.

Note that the Server.lDebugMode flag is new for Web Connection 5.0 and superceeds the #DEFINE DEBUGMODE flag. The DEBUGMODE flag from WCONNECT.H is no longer used by the Web Connection framework.

### Using Error Method instead to capture more detailed Error Information
Notice that you can also still use Web Connection 4.x style Error() methods by using the [wwProcess::lUseErrorMethodErrorHandling](vfps://Topic/_1QK0WYKWN) property and implementing a custom Error() method. The benefit of this approach is that the error is fired in the current CallStack context whereas the TRY/CATCH handler returns control to OnError() only in the RouteRequest() context. The latter doesn't make it possible to capture current variables and ACALLSTACK() info of the actual error. Using an error method provides all the current information of the application at the time of the error.

The documentation on [wwProcess::lUseErrorMethodErrorHandling](vfps://Topic/_1QK0WYKWN) provides more information on how to configure this more complicated mechanism that requires use of #DEBUGMODE compilation flag as in Version 4.x.

### Error Trapping in Template and Script Pages
When debugging embedded FoxPro expressions in HTML code things get a little more tricky. Expression errors are always handled regardless of the Server.lDebugMode flag. This makes it more difficult to debug script and template page errors. 

Templates simply evaluate expressions and code and if an error occurs they embed an error message into the document instead of the result that should have been there:

```html
< % Error: ErrorExpression  % >
```

Expressions are run through the wwEval object and errors are trapped through this mechanism. However, no indication is given as to what caused the error at this point. This method works well for catching errors without doing damage to the system even when evaluating unknown code, but it's not terribly easy to see what the problem is especially if the failure occurs in a code block.

### Hanging the Server?
When error handling is enabled it's next to impossible to crash the wwServer process. There are a few exceptions though: Any operation that brings up a dialog will hang the server. The most annoying one can be a File Open dialog if a file is not found! To avoid File Open dialogs use UNCs rather than drive mappings that might disappear, check for files before opening them or before using them in a SQL statement.


### Checking HTML output and the INI File input
An extremely useful mechanism for [debugging HTTP Request problems](vfps://Topic/Debugging%20Requests) is to use the Status Form's Save Request file option, which saves the last request run. Or you can use the wwProcess::lShowRequestData flag to display the content right on the bottom of your HTML page. When this checkbox is checked the last HTML result and the request input are saved into your temp path specified in the Server setup window as TEMP.HTM and TEMP.INI respectively. You can view this output by clicking on the Display button. The pop up menu lets you view the last request as well as the last HTML output.

Use this feature to check output and make sure that you are returning exactly what you're expecting to return. One important thing to look for is the HTTP header of the resulting HTML document. The INI file input can be very useful in seeing whether all the form variables from a form are retrieved. This lets you see exactly what the server is sending you on each request and lets you access some of the keys that are not exposed directly by the wwCGI class methods.