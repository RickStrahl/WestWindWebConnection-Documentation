The wwSMTP class allows sending emails from Visual FoxPro applications through standard SMTP/ESMTP mail servers. In order to use this class you will need to have an SMTP mail server address from your or your users ISP or a company server.

### Two modes: Classic or wwSmpt (.NET)
The wwSmtp class comes in two flavors: 

* **.NET Mail Mode (nMailMode = 0)**  
Uses .NET and [wwDotNetBridge](VFPS://Topic/Class%20wwDotNetBridge) to send emails. The .NET component provides additional features such as **SSL/TSL** encrypted content, embedded rich content resources like images and the ability to send multiple emails on a single mail connection. The .NET interface uses the native SmtpClient in .NET to abstract mail sending operations which are exposed through wwDotNetBridge.

* **Classic wwIPStuff Mode (nMailMode = 2)**  
Uses classic wwIPStuff behavior using a plain Win32 dll that doesn't need to be registered nor require any special runtimes. Only supports SendMail and SendMailAsync methods - the Connect/SendMessage/Close methods that allow for single connection multi-recipient sends are not available. *This is the default behavior.*

nMailMode can be set via property:

```foxpro
loSmtp = CREATEOBJECT("wwSmtp")
loSmtp.nMailMode = 0 && .NET mode
```

or via Init parameter:

```foxpro
loSmtp = CREATEOBJECT("wwSmtp",0)
```