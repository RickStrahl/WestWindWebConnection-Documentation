Determines what underlying technology is used to send emails

**nMailMode = 0 - .NET** 
Uses .NET to send emails. Specifically wwDotNetBridge.dll is used to call into Westwind.wwSmtp which in turn uses SmtpClient to send emails. This mode supports all the features exposed on the wwSmtp class.

**nMailMode = 2  -  Classic** <small>(default)</small>
Uses the classic Win32 wwIPStuff.dll to send emails. No .NET requirements, but this mode doesn't support various aspects of the wwSmtp class. Specifically there's no support for SSL/TLS encryption or non-disconnecting multi-message sends.

nMailMode can be set via this property or via constructor parameter:

```foxpro
loSmtp = CREATEOBJECT("wwSmtp",0)
```

nMailMode=2 is the default if not specified.