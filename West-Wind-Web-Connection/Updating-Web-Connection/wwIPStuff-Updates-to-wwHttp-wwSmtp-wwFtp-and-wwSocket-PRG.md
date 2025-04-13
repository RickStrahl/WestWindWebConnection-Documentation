In version 5.51 and later wwIPStuff.vcx has been deprecated with all functionality moved to separate PRG classes. This process started some time ago with wwHttp and wwSMTP becoming separate classes. In Version 5.51, wwFtp and wwSocket as well as all the helper functions in wwIPStuff have been moved out of the wwIpStuff.vcx class library.

All wwIPStuff classes now exist as standalone PRG file classes. The classes are:

* wwHttp.prg
* wwSmtp.prg
* wwFtp.prg
* wwSocket.prg

Additionally some of the wwIPStuff 'Internet Utility methods' have migrated into wwAPI.prg as plain, non-class functions:

* EncodeDbf/DecodeDbf
* EncodeFile/DecodeFile
* ZipFiles/UnzipFiles
* GetDomainFromIPAddress/GetIpAddressFromDomain
* InternetDial/InternetHangup
* RasDial/RasHangup

Here are some recommended steps to update:

**1. Replace SET CLASSLIB TO wwIPStuff ADDITIVE**  
The first step is to remove class library loads of wwIPStuff.vcx and replace them with references to the appropriate PRG classes you need.

The old:

```foxpro
SET CLASSLIB TO wwIPStuff ADDITIVE
```

becomes:

```foxpro
SET PROCEDURE TO wwHTTP ADDITIVE
SET PROCEDURE TO wwSmtp ADDITIVE
```

**2. Replace Class Declarations**  
All classes are 100% API compatible with the old wwIPStuff classes so you can easily just replace the class definition. For example:

The old:

```foxpro
SET CLASSLIB TO wwIPStuff ADDITIVE

loIP = CREATEOBJECT("wwIPStuff")
loIP.cMailServer = "mail.myserver.com"
...
loIP.Send()
```

becomes:

```foxpro
SET PROCEDURE TO wwSmtp ADDITIVE

loIP = CREATEOBJECT("wwSmtp")
loIP.nMailMode = 2  && Classic behavior

loIP.cMailServer = "mail.myserver.com"
...
loIP.Send()
```

Note that wwSmtp has two modes (0 - .NET mode 2 - classic wwIPStuff mode) with nMailMode=2 being most compatible with classic operation. Mode 0 uses .NET Interop to send email via wwDotnetBridge.

```foxpro
SET PROCEDURE TO wwHTTP Additive

loIP = CREATEOBJECT("wwHttp")
lcHtml = loIP.HttpGet("http://www.west-wind.com/")
```

**3. wwIPStuff Internet Function Replacement**  
The wwIPStuff class contained a host of Internet and general utility functions which include:

* EncodeDbf/DecodeDbf
* EncodeFile/DecodeFile
* ZipFiles/UnzipFiles
* GetDomainFromIPAddress/GetIpAddressFromDomain
* InternetDial/InternetHangup
* RasDial/RasHangup

All these functions have moved into the wwAPI.prg library as plain functions so:

The old:

```foxpro
SET CLASSLIB TO wwIPStuff.vcx Additive

loIP = CREATEOBJECT("wwIPstuff")
lcDomain = loIP.GetDomainFromIP("209.216.162.15")
lcIp = loIP.GetIpFromDomain("www.west-wind.com")
```

becomes:

```foxpro
SET PROCEDURE TO wwAPI ADDITIVE

lcDomain = GetDomainFromIP("209.216.162.15")
lcIp = GetIpFromDomain("www.west-wind.com")
```

### VCX Class issues on Forms
wwIPStuff and wwFTP were VCX classes and as such could be dragged and dropped onto forms and visual components. This is no longer possible with the PRG based classes which have to be manually attached. To do this use ADDOBJECT() in the INIT() of the form or component to add the component in code:

```foxpro
FUNCTION INIT()

THIS.AddObject("oHttp","wwHttp")

* **later in code anywhere else in the class/form/component
lcHTML = THIS.oHttp.HttpGet("http://www.west-wind.com")

ENDFUNC
```

In addition the wwHTTP and wwFTP classes include OnHttpBufferUpdate and OnFtpBufferUpdate methods that could simply be implemented in Form/Control code simply by overriding the method. This is also no longer directly possible as PRG classes don't expose their methods directly through the designer.

However you can use BINDEVENT() to bind to these events and implement these methods on the Form/Control as before:

```foxpro
FUNCTION INIT()

THIS.AddObject("oHttp","wwHttp")

BINDEVENT(this.oHTTP,"OnHttpBufferUpdate",THISFORM,"OnHttpBufferUpdate")

* **later in code anywhere else in the class/form/component
lcHTML = THISFORM.oHttp.HttpGet("http://www.west-wind.com")

ENDFUNC
```

You can then implement the OnHttpBufferUpdate method on the form:

```foxpro
FUNCTION OnHttpBufferUpdate
LPARAMETER lnBytesDownloaded,lnBufferReads,lcCurrentChunk, loHTTP

* **Handle the event here

ENDFUNC
```

### Suggestions on how to effectively find and replace
All of the above are fairly straight forward fixes that can be made fairly quickly. The key to the changes is to *find* the code where these operations occur.

The quickest way to find things *project wide* is to use the VFP CodeReferences tool from the Tool menu. Code References let you search for strings in your code with plain text or RegEx expressions. Finding code related to wwIPStuff is fairly easy to do.

If you only use wwIPStuff in a few places start with "wwIPStuff" (or a constant if that's what you are using to instantiate the class) for searching. This should find you the block of code where wwIPStuff is used and replace the class declaration with the appropriate PRG class (wwHttp,wwFtp, wwSocket). I found it easiest to simply hand edit these blocks and replacing the class names manually.

For the wwIPStuff internet and utility functions like EncodeDbf/DecodeDbf/GetDomainNameFromIp etc. you should search for each of the methods and make sure your remove the object prefix (ie. loIP.EncodeDbf() to just EncodeDbf()).