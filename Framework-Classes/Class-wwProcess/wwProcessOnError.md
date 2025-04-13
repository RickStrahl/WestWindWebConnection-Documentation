Occurs when an error occurs in the Web Connection Process handling and Server.lDebugMode is set to .T.

This method is the core error handler in Web Connection that gets fired when any error in executing code occurs. This event is fired from a TRY CATCH block in the main Process method of the class and forwards its exception object to this OnError method.

The default error handler performs the following tasks:
* Creates an HTML error page that describes the error. It call [wwProcess::ErrorMsg](vfps://Topic/wwProcess%3A%3AErrorMsg) to do so.
* Tries to log the error into the server request log.
* Tries to send an email to the administrator to report the error.

The default implementation of this method displays a descriptive error message to the user and calls LogError to log the error and fire administrative emails. If you override this method for custom error behavior make sure that you call [wwProcess::LogError](vfps://Topic/_1NA0ODUSQ) from your code.

The default implementation looks like this and gives you an idea what error information is available for your custom OnError handlers:

```foxpro
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** **
* wwProcess :: OnError
* *** *** *** *** *** *** *** *** *** *** *** *** ***
* **  Function: Called when an error occurs and Server.lDebugMode is off.
* **    Assume:
* **      Pass: loException
* **    Return: .T. if you completely handled the error including output
* **            generation. Else return .F.
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** **
FUNCTION OnError(loException as Exception)


* ** Shut down this request with an error page - SAFE MESSAGE (doesn't rely on any objects)
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

* ** Log the Request
IF TYPE("THIS.oServer")="O" AND !ISNULL(THIS.oServer)
   this.LogError(loException)
ENDIF

* ** We've completely handled the error!
RETURN .T.
ENDFUNC
*  wwProcess :: OnError
```