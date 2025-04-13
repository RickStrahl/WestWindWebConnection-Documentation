This method allows you send large files to the client without having to use Response.Write() to load these files into a string in Visual FoxPro. 

This method can get around the 16 meg limitation of Visual FoxPro strings for request output in COM mode. TransmitFile is much more efficient at sending large files as it avoids loading both FoxPro and the ISAPI DLL with these large strings, but instead causes wc.dll to read output directly from file back to the client.

You can send files directly:

```foxpro
FUNCTION TransmitFile

Response.TransmitFile("d:\sailbig.jpg","image/jpeg")

RETURN
```

Or you can generate output dynamically using a separate Response object that writes to file (or any other mechanism that writes a file for that matter) like wwResponseFile.

```foxpro
FUNCTION HugeOutput
LOCAL lcOutputFile, loResponse

lcOutputFile = Server.oConfig.oWwDemo.cHtmlPagePath + ;
               "temp\" + SYS(2015) + ".htm"

* ** Create a separate response object
loResponse = CREATEOBJECT("wwResponseFile",lcOutputFile)

FOR x=1 TO 1000000
   loResponse.Write("012345678901234567890" + "<br>")
ENDFOR

* ** Release and close
loResponse = .null.

Response.TransmitFile(lcOutputFile,"text/html") * ** Some housekeepingDeleteFiles(JustPath( lcOutputFile ) + "\_*.*",900)  && timeout in 15 minutes
ENDFUNC
```