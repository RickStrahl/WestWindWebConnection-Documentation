Allows capturing of errors that occur during callback method processing, since callback method errors are independently handled inside of a TRY/CATCH. 

This method gives you the opportunity to be notified of an error that occurs and you can capture the error by using BINDEVENT() in your page (or whereever). 

```foxpro
* ** Page code
FUNCTION OnInit()
BINDEVENT(this.AjaxCallback,"OnError",this,"OnCallbackError")
ENDFUNC

FUNCTION OnCallbackError(loException)
* Do something with the exception - log, re-throw etc.
ENDFUNC
```