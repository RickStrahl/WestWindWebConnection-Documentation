Allows sending large files directly from disk, bypassing FoxPro processing and instead directly serving the file very efficiently. By doing so you can easily send very large files and bypass FoxPro's 16mb memory limitations and without tying a FoxPro Web Connection instance.

> Transmit file serves the file **as content that is displayed in the browser** if the browser supports an in browser viewer. If you need to download a file and save it explicitly you can use [Response.DownloadFile()](VFPS://Topic/_1O80YQ3I0) to download a file and force it to be opened with a *Save As* File dialog using the same TransmitFile semantics. 

You can send files directly using a **full path**:

```foxpro
FUNCTION SendLogo
Response.TransmitFile("c:\sites\MySite\Images\sailbig.jpg","image/jpeg")
RETURN
```
or using a **site relative virtual path**:

```foxpro
FUNCTION SendLogo
Response.TransmitFile("~/images/sailbig.jpg","image/jpeg")
RETURN
```

You can also generate output dynamically to a file on disk. This is a good use case for the wwResponseFile class:

```foxpro
FUNCTION HugeOutput
LOCAL lcOutputFile, loResponse

*** Translate virtual path to physical path
lcOutputFile = THIS.ResolvePath("~/temp/ + SYS(2015) + ".htm")

*** Create a separate response object that writes to file
loResponse = CREATEOBJECT("wwResponseFile",lcOutputFile)

FOR x=1 TO 1000000
   loResponse.Write("012345678901234567890" + "<br>")
ENDFOR

*** Release and close
loResponse = .null.

Response.TransmitFile(lcOutputFile,"text/html")

*** Some housekeeping
DeleteFiles(JustPath( lcOutputFile ) + "\_*.*",900)  && timeout in 15 minutes

ENDFUNC
```