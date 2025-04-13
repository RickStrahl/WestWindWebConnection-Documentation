An FTP and FTPS client that allows you to access FTP servers and perform common FTP transfer and management operations.

Uses the <a href="https://github.com/robinrodricks/fluentftp" target="top">Fluent FTP</a> .NET library.

```foxpro
DO wwFtpClient && Load library and dependencies  
loFtp  = CREATEOBJECT("wwFtpClient")

loFtp.cServer = "someserver.com"
loFtp.nPort = 23        && only needed with custom ports (default is 21)
loFtp.lUseTls = .T.     && Secure Connection
loFtp.cUsername = "crankyFun221"
loFtp.cPassword = "superSeekrit#9"

IF !loFtp.Connect()
	? loFtp.cErrorMsg
	RETURN
ENDIF
? "Connected to " + lcServer	

IF !loFtp.DownloadFile("Tools/jsMinifier.zip", "c:\temp\jsMinifier.zip")
	? loFtp.cErrorMsg
	RETURN
ENDIF	
? "Downloaded " + "Tools/jsMinifier.zip"

lcUploadFile = "Tools/jsMinifier" + SYS(2015) + ".zip"
IF !loFtp.UploadFile("c:\temp\jsMinifier.zip", lcUploadFile)
	? loFtp.cErrorMsg
	RETURN
ENDIF
? "Uploaded " + lcuploadFile

*** provide a folder name (no wildcards)
loCol = loFtp.ListFiles("/Tools")
? TRANSFORM(loCol.Count ) + " matching file(s)"
? loFtp.cErrorMsg
FOR EACH loFile IN loCol FOXOBJECT
   IF ( AT("jsMinifier_",loFile.Name) = 1)
	   ? loFtp.oBridge.ToJson(loFile)  && print out file obj as json
	   IF loFtp.DeleteFile(loFile.FullName)
	      ? "Deleted " + loFile.FullName
	   ENDIF
	   
   ENDIF
ENDFOR

loFtp.Close()
```