You may want to send messages to recipient in text AND HTML format, so that they can read the content even if the Mail reader is not HTML capable.

```foxpro
LOCAL loSMTP as wwSmtp
loSmtp=CREATEOBJECT("wwSmtp")
* loSmtp.nMailMode = 2  && wwIPStuff mode (Win32)   0 - .NET wwSmtp

loSmtp.cMailServer="mail.yourmailserver.net"
loSmtp.cSenderEmail="rstrahl@west-wind.com"
loSmtp.cSenderName="Rick Strahl"

loSmtp.cRecipient="somebody@sweat.com,another@bust.com"
loSmtp.cSubject="wwIPStuff Test Message"

*** Set up Messages - first part is the most desirable content type
loSmtp.cMessage="<b>This is the HTML message text</b>"
loSmtp.cContentType = "text/html"

*** Second part is plain text as fallback
loSmtp.cAlternateText = "This is the plain message text"
loSmtp.cAlternateContentType = "text/plain"

llResult = loSmtp.SendMail()      
IF !llResult
   Wait window loSmtp.cErrorMsg
ENDIF
```

cContentType determines the content type of the main message, while cContentTypeAlternate applies to the alternate content. It's recommended that you your most desirable content type first because that's what the mail client looks for first - it goes down the list and uses the first it knows how to handle.

Please note that you can't use file attachments in this mode when using nMailMode=2 (wwipstuff.dll).