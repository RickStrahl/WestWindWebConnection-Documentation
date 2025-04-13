FTP functionality in are supported by two classes:

* [wwFtpClient](VFPS://Topic/_6WP0MRZ80) - non-secure FTP and FTPS (FTP over TLS)
* [wwSftpClient](VFPS://Topic/_6WR0ZM6JD) - SFTP over SSH

Both classes use the same interface, and can be used interchangeably. The following example uses FTPS.

### Downloading a File

```foxpro
DO wwFtpClient && Load Library

loFtp  = CREATEOBJECT("wwFtpClient")
loFtp.lUseTLS = .T.

loFtp.cServer = "someserver.com"  && domain or IP address (can also include a port)
loFtp.nPort = 23   && only needed with custom ports
loFtp.cUsername = "crankyFun221"
loFtp.cPassword = "superSeekrit#9"

IF !loFtp.Connect()
	? loFtp.cErrorMsg
	RETURN
ENDIF
? "Connected to " + lcServer	

IF !loFtp.DownloadFile("/Tools/jsMinifier.zip", "c:\temp\jsMinifier.zip")
	? loFtp.cErrorMsg
	RETURN
ENDIF	
? "Downloaded " + "Tools/jsMinifier.zip"

loFtp.Close()
```

> **Note**: You can perform multiple FTP operations with an open connection between the `Connect()` and `Close()` calls.

### How to specify FTP Paths
FTP paths tend to use Unix directory conventions so you use `/` slashes. We recommend you use absolute server pathing starting the `/` root path, but all paths also support relative paths based. Both of these are valid for the remote file:

* /Tools/jsMinifier.zip   (server root relative)
* Tools/jsMinifier.zip    (current (logon) path relative)

### Uploading a File
To upload a file works virtually identically:

```foxpro
*** ... same setup as previous example

IF !loFtp.Connect()
	? loFtp.cErrorMsg
	RETURN
ENDIF
? "Connected to " + lcServer	

lcRemoteFile = "Tools/jsMinifier" + SYS(2015) + ".zip"
IF !loFtp.UploadFile("c:\temp\jsMinifier.zip", lcRemoteFile)
	? loFtp.cErrorMsg
	RETURN
ENDIF	
? "uploaded: " + lcRemoteFile

loFtp.Close()
```

### Listing Files from a Remote Directory