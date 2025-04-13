Sends a single message to an SMTP server, but allows multiple messages to be sent on a single open SMTP connection. Requires that you call Connect() before SendMessage() calls and Close() or Dispose() after.

Unlike SendMail this operation is meant for multiple messages to be sent where a connection is opened and then multiple messages are sent, then the connection is closed:

```foxpro
loSmtp.Connect()

*** Send many messages to different addresses
SCAN 
   ? loSmtp.SendMessage( TRIM(Email) )
   ? loSmtp.cErrorMsg
ENDSCAN

loSmtp.Close()
```

Note that unlike SendMail() this method accepts the Recipient, CC and BCC values as input parameters since these might be changing for each message sent. All remaining properties are the same.