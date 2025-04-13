FTP functionality in these tools are supported by two classes:

* [wwFtpClient](VFPS://Topic/_6WP0MRZ80) - non-secure FTP and FTPS (FTP over TLS)
* [wwSftpClient](VFPS://Topic/_6WR0ZM6JD) - SFTP over SSH

Both classes use the same interface, and can be used interchangeably.  The following example uses FTPS:

```foxpro
loFtp  = CREATEOBJECT("wwFtpClient")

loFtp.cServer = "someserver.com"
loFtp.nPort = 23   && only needed with custom ports
loFtp.cUsername = "crankyFun221"
loFtp.cPassword = "superSeekrit#9"

IF !loFtp.Connect()
	? loFtp.cErrorMsg
	RETURN
ENDIF
? "Connected to " + lcServer	


lcUploadFile = "Tools/jsMinifier" + SYS(2015) + ".zip"
IF !loFtp.UploadFile("c:\temp\jsMinifier.zip", lcUploadFile)
	? loFtp.cErrorMsg
	RETURN
ENDIF
? "Uploaded " + lcuploadFile

loFtp.Close()
```

You can also use the lower level method [FTPSendFileEx](vfps://Topic/wwftp%3A%3AFTPSendFileEx) which allows more control over how the file is sent. This method allows for message events to be fired that you can catch and allow you to display status information. For details see the documentation for this method. This method also allows you to send multiple files to be sent without reconnecting for each file. The basic syntax for this method is:

```foxpro
loFtp=CREATEOBJECT("wwFTP")

loFtp.FTPConnect("ftp.west-wind.com")
loFtp.FTPSendFileEx("c:\temp\pkzip.exe","/downloads/pkzip.exe")
loFtp.FTPSendFileEx("c:\temp\pkunzip.exe","/downloads/pkunzip.exe")
loFtp.FTPClose()

*** You can also use FTPSendFileEx2 if you run into problems with FTPSendFileEx
loFtp.FTPConnect("ftp.west-wind.com")
loFtp.FTPSendFileEx2("c:\temp\pkzip.exe","/downloads/pkzip.exe")
loFtp.FTPSendFileEx2("c:\temp\pkunzip.exe","/downloads/pkunzip.exe")
loFtp.FTPClose()
```

> #### Why FTPSendFileEx2?
> There's another method FTPSendFileEx2() that has the same signature as FTPSendFileEx() and uses a different WinInet API that is more reliable. If you run into any issues with FtpSendFileEx - occasional dropped connections or upload aborts - try usiong FTPSendFileEx2(). This method does not expose the event postings to let you know upload progress though, so FTPSendFileEx() is still the better choice UNLESS you run into problems.