Web Connection does not have support for full request and response logging. The built in logging mechanism logs to a data table (Fox or SQL) but it doesn't log response data. The reason for this is that response data is always large and would cause the file to blow over the 2gig limit pretty quickly. So no response logging is built in.

But if you really want to log output override you can override the `YourServer::SaveRequestFiles` method in your Web Connection Server class. 

The base method has code that extracts the request and response data and writes it out to a request file. You can use this very same logic and instead of writing it out to a couple of files, write it into a single appendable log file:


```foxpro
*** NOTE: Must have `SaveRequestFiles` flag enabled in app.ini
FUNCTION SaveRequestFiles
LOCAL lcOutput, lcRequest

*** If you want the original SaveRequestFiles functionality for the UI to work
*** Slower since the code here is basically duplicated and writes yet another file
* DODEFAULT()

lcOutput = ""
IF THIS.lcomobject
   lcOutput = this.cOutput
ELSE
   IF FILE(THIS.oRequest.cOutputFile)
      lcOutput = File2Var(THIS.oRequest.cOutputFile)
   ENDIF
ENDIF

lcRequest =  THIS.oRequest.cServerVars
IF LEN(this.oRequest.cFormVars) > 1
	lcRequest = lcRequest + POST_BOUNDARY + ;
                THIS.oRequest.cFormVars
ENDIF

LogString( CHR(13) + "--- Request ---" + CHR(13) + lcRequest + CHR(13) +;
	 "--- Response ---" + CHR(13) + ;
	 lcOutput + CHR(13) + ;
	 "--- End Request ---" + CHR(13),;
	 "temp\SaveRequestFiles.log")

ENDFUNC
```