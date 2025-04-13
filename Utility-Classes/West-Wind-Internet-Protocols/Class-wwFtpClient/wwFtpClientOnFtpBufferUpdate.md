A progress event that is fired as files are downloaded or uploaded providing basic progress information as the upload proceeds.

To make this work you need to:

* Create an Event Handler Class that implements `OnFtpBufferUpdate()`
* Assign it to the FTP Object's `loFtp.oProgressEventObject`


```foxpro
loFtp.oProgressEventObject = CREATEOBJECT("FtpProgress")

*** assignment must happen BEFORE .Connect
loFtp.Connect()

...
DEFINE CLASS FtpProgress as Custom

FUNCTION OnFtpBufferUpdate(lnPercent, lnDownloadedBytes, lcRemotePath, lcMode)
  lcMsg = lcMode + ": " + TRANSFORM(lnPercent) + "% complete. " + lcRemotePath + " - " + TRANSFORM(lnDownloadedBytes) + " bytes"
  ? "*** " + lcMsg
ENDFUNC

ENDFUNC
```