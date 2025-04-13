The following example demonstrates sending an email with attachments via GMail.

> In order to send TLS/SSL Emails you **have to use the .NET mode** of `wwSmtp`. To do this make sure you set `loSmtp.nMailMode=0`.

Note that this should also work with other providers like **Outlook.com** or any provider that requires SSL based SMTP connections.

```foxpro
DO wwSmtp 

LOCAL loSmtp as wwSmtp
loSmtp = CREATEOBJECT("wwSmtp")

loSmtp.nMailMode = 0  && .NET Mode required

*** Google SMTP server - requires login and SSL/TLS
loSmtp.cMailServer = "smtp.gmail.com:587"

*** Important for Secure SSL/TLS connections
loSmtp.lUseSsl = .T.

loSmtp.cUsername = "yourEmail12@gmail.com"  && or whatever uid is
loSmtp.cPassword = "yourpassword"

loSmtp.cSenderName= "wwIPstuff Samples"
loSmtp.cSenderEmail = "support@mydomain.com"

loSmtp.cRecipient = ["Jimmy" <jimmyd@mydomain.com>]

*** Actual message properties
loSmtp.cSubject = "wwSMTP Test Message"
loSmtp.cMessage = "<b>HTML Test message from wwsmtp, to check out how this operation works.</b>"

*** If cMessage contains HTML set cContentType to text/html
loSmtp.cContentType = "text/html"


*** Optional To add attachments use Attachment and point at a full file path
loSmtp.AddAttachment("c:\sailbig.jpg")
loSmtp.AddAttachment("c:\sailbig_original.jpg","image/jpeg")


llResult = loSmtp.Sendmail()
IF llResult
    WAIT WINDOW "Mail sent..." nowait
ELSE
    ? "Mail sending failed: " 
    ? loSmtp.cErrorMsg
ENDIF	
```

> #### @icon-warning Gmail Configuration Needed
> Note that if you have GMail **business account** (ie. you pay for Gmail) you'll need to enable [Enforce Access to less secure Apps for all Users](https://support.google.com/a/answer/6260879?hl=en) in order to allow external SMTP clients to send email through GMail. This might change for **all** Gmail accounts in the future. *Update: Google now has made it just about impossible to use SMTP to send any longer via 'insecure apps' unless individual accounts are specifically configured with a nearly inaccessible property. Bottom line: Don't use Gmail for SMTP*