This class provides SFTP (Secure FTP over SSH) support for connecting to SFTP servers.

```foxpro
DO wwSftpClient
DO wwutils  && for UI helpers only

*** Using Rebex Tiny SFTP Server for local testing
lcServer = "127.0.0.1"
lnPort = 23
lcUsername = "tester"
lcPassword = "password"

CLEAR

? "***  wwSftpClient Test***"
loFtp  = CREATEOBJECT("wwsFtpClient")
loFtp.cLogFile = "c:\temp\ftp.log" && verbose log - leave empty
loFtp.lIgnoreCertificateErrors = .T.   && self-signed cert not installed

loFtp.cServer = lcServer
loFtp.nPort = lnPort
loFtp.cUsername = lcUsername
loFtp.cPassword = lcPassword


*** Optional Progress Events - clas below
loFtp.oProgressEventObject = CREATEOBJECT("SFtpClientProgressEvents")


IF !loFtp.Connect()
	? loFtp.cErrorMsg
	RETURN
ENDIF
? "Connected to " + lcServer	


IF !loFtp.DownloadFile("SubFolder/Sail-Big.jpg", "c:\temp\Sail-Big.jpg")
	? loFtp.cErrorMsg
	RETURN
ENDIF	
? "Downloaded " + "SubFolder/Sail-Big.jpg"

lcUploadFile = "SubFolder/Sail-Big-UPLOADED" + SYS(2015) + ".jpg"
IF !loFtp.UploadFile("c:\temp\Sail-Big.jpg", lcUploadFile)
	? loFtp.cErrorMsg
	RETURN
ENDIF
? "Uploaded " + lcuploadFile


*** provide a folder name (no wildcards)
loCol = loFtp.ListFiles("/SubFolder")
IF ISNULL(loCol)
   ? loFtp.cErrorMsg
   RETURN
ENDIF

? TRANSFORM(loCol.Count ) + " matching file(s)"
? loFtp.cErrorMsg
FOR EACH loFile IN loCol FOXOBJECT
   ? loFile.Name
   
   IF ( AT("Sail-Big-UPLOADED",loFile.Name) = 1)
	   *? loFtp.oBridge.ToJson(loFile)  && for kicks print out as json
	   IF loFtp.DeleteFile(loFile.FullName)
	      ? "*** Deleted " + loFile.FullName
	   ENDIF
   ENDIF
ENDFOR

loFtp.Close()
RETURN


DEFINE class SFtpClientProgressEvents as Custom

*** You can set this to .T. to abort an upload or download
lCancelDownload = .F.

FUNCTION OnFtpBufferUpdate(lnPercent, lnDownloadedBytes, lcRemotePath, lcMode)
  lcMsg = lcMode + ": " + TRANSFORM(lnPercent) + "% complete. " + lcRemotePath + " - " + TRANSFORM(lnDownloadedBytes) + " bytes"
  ? "*** " + lcMsg
ENDFUNC

ENDDEFINE 
```