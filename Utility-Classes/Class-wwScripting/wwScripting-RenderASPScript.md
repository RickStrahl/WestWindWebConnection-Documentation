Renders an ASP style page as a compiled VFP program. This method
handles creation of the VFP source code from the script file,
compilation and checking for updates of the file.

Parsing works using ASP style syntax:

```html
<%= Expression  %>

<%
VFP Code Block
%>
```

The script page gets passed a Response object which you can
USE to send output directly into the output stream.

### Using the wwScripting Class to Parse FoxPro ASP Script
The following is an example of how to create the wwScripting instance and use it to render ASP style scripting. This compiles the code into a PRG file and executes the resulting PRG file.

First here's a reusable method to create an instance of wwScripting setup for script execution:

```foxpro
************************************************************************
FUNCTION CreatewwScripting(llDebugMode)
****************************************

loScript = CREATEOBJECT("wwScripting")

*loScript.cScriptResponseClass = "wwPageResponse"
loScript.lNoVersionCheck = .F.
loScript.lEditErrors = .F.
loScript.lSaveVfpSourceCode = .F.
loScript.lShowFullErrorInfo = .F.
loScript.cBasePath = "c:\webconnection\fox\tests"

*** Write output directly into this Response object
*loScript.oResponse = CREATEOBJECT("wwPageResponse")

IF llDebugMode
	loScript.lShowFullErrorInfo = .T.
	loScript.lEditErrors = .T.
	loScript.lStopOnError = .T.
ELSE
    *** We want the exception to bubble up to Web Connection error handler
    loScript.lThrowException = .T.	
ENDIF

RETURN loScript
ENDFUNC
*   CreatewwScripting
```

And here is a simple Test method that uses this to execute the script code:

```foxpro
************************************************************************
FUNCTION TestwwScripting
****************************************

lcText = "This " +CHR(10)+ "is one line of text" + CHR(10) + " separated by CHR(10)s <%= VERSION() %>" + CHR(10) + CHR(10) 

TEXT TO lcText2 NOSHOW
<%  x = 1 
    x = x + 2
%>

Value is: <%= x %>    
ENDTEXT

lcText = lcText + lcText2

lcFile = "c:\webconnection\fox\tests\test.wcs"
STRTOFILE(lcText,lcFile)

loScript = THIS.CreatewwScripting()
loScript.lSaveVfpSourceCode = .t.

*** Create output from the rendered script as a string
lcOutput = loScript.RenderAspScript(lcFile)

THIS.MessageOut(lcOutput)
ENDFUNC
*   TestwwScripting
```