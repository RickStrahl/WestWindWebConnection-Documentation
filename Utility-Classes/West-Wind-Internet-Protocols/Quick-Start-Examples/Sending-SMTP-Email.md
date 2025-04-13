wwSmtp easily supports sending email over the Internet. Use the following syntax for using the mail functionality is the same:

```foxpro
DO wwSmtp && load libraries

LOCAL loSMTP as wwSmtp
loSmtp=CREATEOBJECT("wwSmtp")
loSmtp.nMailMode = 0  &&   0 - .NET wwSmtp (defult), 2 - classic wwIPStuff mode  (no-SSL)

loSmtp.cMailServer="mail.yourserver.net"   &&  with port: mail.yourserver.net:547
loSmtp.cSenderEmail="rstrahl@mydomain.com"
loSmtp.cSenderName="Mr. Testa"

*** Optional Username and Password (usually required these days)
* loSmtp.cUsername = "ricks"
* loSmtp.cPassword = "secret"

*** Optional SSL/TLS connection (Usually required these days)
* loSmtp.lUseSsl = .T.

loSmtp.cRecipient="somebody@sweat.com,another@bust.com"
loSmtp.cCCList="rstrahl@east-wind.com,looser@nobody.com"
loSmtp.cBCCList="fred@flintstone.com"
loSmtp.cSubject="wwSmtp Test Message"

*** Optionally specify content type - text/plain is default
loSmtp.cContentType = "text/html"  
loSmtp.cMessage="Who said this had to be <b>difficult</b>?"

*** Optionally provide an alternate content type or plain text fallback
loSmtp.cAlternateContentType = "text/plain"
loSmtp.cAlternateText = "Plain text can go here."

*** Optionally send file attachments
loSmtp.AddAttachment("c:\temp\somefile.pdf")
oSmtp.AddAttachment("c:\temp\sailbig.jpg")

llResult = loSmtp.SendMail()      

IF !llResult
   Wait window loSmtp.cErrorMsg
ENDIF
```

You can also send messages Asynchronously:

```foxpro
*** Optionally send Asynchronous without waiting for a result
loSmtp.SendMailAsync()
```

Note that when you send messages asynchronously, you get no result code or other indication whether the mail sending was a success.

### Dependencies for wwSmtp
External dependencies depend on the nMailMode property setting. 

**nMailMode = 0**  
.NET 2.0 or later Runtime installed
wwDotNetBridge.dll (in Fox Path)
wwIPStuff.dll (in Fox Path)

**nMailMode = 2**  
wwIPstuff.dll  (in Fox Path)

### Errors and Logging
Sending Emails can cause many errors on the server if your connection isn't configured properly or if you're not providing the right security information. Many common errors can pop up. In general when an error occurs wwIPStuff returns the SERVER ERROR MESSAGE. This is important - these errors typically have a 3 digit error code and a message associated with that is displayed in raw format. The messages give the server's reason for not accepting or failing the opeation. This error message is returned to you in the cErrorMsg property which you can check after the SendMail() call:

```foxpro
IF !loSmtp.SendMail()
	wait window loSmtp.cErrorMsg
	RETURN
ENDIF
```

### Inspecting the SMTP Connection
If you can't figure our what's going wrong with your SMTP connection you might want to look at the raw TCP/IP log. It's available by setting the `.cLogFile` property, **but it only works in classic, non-.NET mode**. The .NET Component uses .NET's internal SMTP processing which uses a different more complicated Trace log that's not all that useful.

Instead I would recommend if you really want to look at the SMTP traffic
Note: Logging is available only in `.MailMode=2` (wwIPStuff). For .NET based logging use the built in .NET System.Tracing logging features.