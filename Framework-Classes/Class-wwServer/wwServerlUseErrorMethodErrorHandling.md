Flag that allows you to bypass TRY/CATCH error handling and instead implement your own Error() method on the Process class for error handling.

This flag is primarily meant as a backwards compatibility feature for developers who've built extensive error handling into their existing pre-5.0 Web Connection applications. It's also meant to provide richer error information for error handlers. One side effect of TRY/CATCH error handling is that the FoxPro call stack is unwound so when the default OnError method is called in your code, all the code information (PRIVATE and LOCAL vars, ASTACKINFO() etc.) doesn't reflect the call stack of the time of the error. You do get this functionality in an error method.

When lUseErrorMethodErrorHandling=.T., TRY\CATCH handling is not enabled even if the Server.lDebugMode flag is set to .F. Note this flag must be set prior to the call to Process() which means immediately after CREATEOBJECT(). The flag is used inside of the Process() call and before OnProcessProcess() is called.

To implement a custom error methods we recommend using a #DEBUGMODE flag in WCONNECT_OVERRIDE.h and then writing code like this:

```foxpro
* ** In YourServer::OnInit
FUNCTION OnInit
...
lUseErrorMethodErrorHandling = .t.
ENDFUNC


* ** In all your Process classes
#IF !DEBUGMODE
FUNCTION Error(lnerror,lcMethod,lnLine)

* ** Do whatever logging, email etc. you need to 

RETURN TO ROUTEREQUEST
ENDFUNC
#ENDIF
```

Note the call to RETURN TO ROUTEREQUEST which is required to return execution back to the calling top level handler and complete the request. This is the way error handling worked in versions prior to Web Connection 5.0.

To still take advantage of stock error handling you can do something similar to the following:

```foxpro
#IF !DEBUGMODE
FUNCTION Error(lnerror,lcMethod,lnLine)
LOCAL loException as Exception

loException = CREATEOBJECT("Exception")
loException.LineNo = lnLine
loException.LineContents = MESSAGE(1)
loException.ErrorNo = lnError
loException.Message = MESSAGE()
loException.Procedure = SYS(16)

* ** Call stock error handling mechanism so we don't duplicate work
* ** Stock handler now has full access to current call stack
this.OnError(loException)

RETURN TO RouteRequest
ENDFUNC
#ENDIF
```

which routes the callback to the OnError method which provides the default error handling in Web Connection that can be overridden. The RETURN TO then returns back in the mainline code.