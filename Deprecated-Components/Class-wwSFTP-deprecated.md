Please uses the [wwSftpClient Class](VFPS://Topic/_6WR0ZM6JD) instead.

This class provides an **SFTP** client that works with **SSH** based secure FTP connections. This is the most common secure FTP format used for *nix based servers and most widely supported.

> #### @icon-warning Does not support FTPS
> SFTP and FTPS are two completely separate protocols and **this library only supports SSH based SFTP**, not FTPS which is FTP over TLS.

This class is based on wwFtp and supports the same interface for the most part - there are subtle differences in how directory listings are handled (you should only pass in paths, not wildcards which are invalid for the SSH client). Like wwFtp this class supports both high level and lower level routines for sending files.

There are two types of operations:

* **High Level Self-Contained Operations**  
Specifically `FtpGetFile()` and `FtpSendFile()` which open a connection and send/receive a file, then close the connection. They are self-contained and don't require an explicit call to open and close connections.

* **Low Level Operations - Require an Open Connection**  
All other operations are connection based, meaning they require that a connection created with `FtpConnect()` is open **before** you call the low level operations. You can run multiple low level operations on the same open connection.

### High Level Upload and Download
The High level functions are easy functions to upload and download files `FtpGetFile()` and `FtpSendFile()` as they are single commands that don't require explicitly opening and closing of connections.

```foxpro
loFtp = CREATEOBJECT("wwSftp")
loFtp.nFtpPort = 23

lcHost = "127.0.0.1"
lnPort = 23
lcUsername = "tester"
lcPassword = "password"

*** Download
lcOutputFile = ".\tests\sailbig.jpg"
DELETE FILE lcOutputFile

lnResult = loFtp.FtpGetFile(lcHost,"sailbig.jpg",".\tests\sailbig.jpg",1,lcUsername,lcPassword)

this.AssertTrue(lnResult == 0,loFtp.cErrorMsg)
this.AssertTrue(FILE(lcOutputFile))


*** Upload a file
lcSourceFile = ".\tests\sailbig.jpg"
lcTargetFile = "Sailbig2.jpg"

lnResult = loFtp.FtpSendFile(lcHost,lcSourceFile,lcTargetFile,lcUsername,lcPassword)
this.AssertTrue(lnResult == 0,loFtp.cErrorMsg)
```

### Low Level Functions
The lower level functions require that you set properties on the wwSFTP object, create a connection and then use the individual methods to send and receive files, list and change directories, delete files, create folders etc. The advantage of this mechanism is that you have more control over the process as well as keeping a connection open between requests which is more efficient for sending multiple files.

The following is an integration test that demonstrates a host of the lower level features:

```foxpro
loFtp = CREATEOBJECT("wwSftp")

loFtp.cFtpServer =  "127.0.0.1"
loFtp.nFtpPort = 23
loFtp.cUsername = "tester"
loFtp.cPassword = "password"

loFtp.FtpConnect()

*** Change to a specific folder - convenience only - you can reference relative paths
this.AssertTrue(loFtp.FtpSetDirectory("subfolder"),loFtp.cErrorMsg)

*** Create a new directory
this.AssertTrue(loFtp.FtpCreateDirectory("SubFolder2"),loFtp.cErrorMsg)

*** Send a file into the new directory
this.AssertTrue(loFtp.FtpSendFileEx(".\tests\sailbig.jpg","subfolder2/sailbig.jpg")==0,loFtp.cErrorMsg)

*** Download the file just uploaded
this.AssertTrue(loFtp.FtpGetFileEx("subfolder2/sailbig.jpg",".\tests\sailbig2.jpg")==0,loFtp.cErrorMsg)

*** Delete it
this.AssertTrue(loFtp.FtpDeleteFile("subfolder2/sailbig.jpg")==0,loFtp.cErrorMsg)

*** And delete the folder
this.AssertTrue(loFtp.FtpRemoveDirectory("Subfolder2"),loFtp.cErrorMsg)
```