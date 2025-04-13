The `wwDotnetCoreBridge` is a subclass of wwDotnetBridge class that **shares the same features as the `wwDotnetBridge` class**, but works against .NET Core Runtimes. After initial loading of the runtime, both libraries have the same feature set. For detailed member documentation please see the [wwDotnetBridge class](VFPS://Topic/_24N1CFW3A).

`wwDotnetCoreBridge` loads the **32 bit .NET Core Runtime** and provides a class factory and proxy implementation to create instances and access features that COM does not natively expose. 

The only difference between `wwDotnetBridge` and `wwDotnetCoreBridge` is how they are instantiated. `wwDotnetCoreBridge` is instantiated like this:

```foxpro
*** Default: Uses highest .NET Core 32 bit Runtime installed
loBridge = CREATEOBJECT("wwDotnetCoreBridge")

*** Or use cached version: One instance that gets cached
loBridge = GetwwDotnetCoreBridge()
```

or you can specify a specific .NET Core 32 bit Runtime version number or runtime installation path:

```foxpro
*** By version number
loBridge = CREATEOBJECT("wwDotnetCoreBridge","6.0.5")  && or partial versions 6, 6.0 etc.

*** By Runtime Folder 
*** - allows for private runtime or self-contained application installs
loBridge = CREATEOBJECT("wwDotnetCoreBridge",
              "C:\Program Files (x86)\dotnet\shared\Microsoft.NETCore.App\5.0.17")
```

For all features, please refer to the main [wwDotnetBridge documentation](VFPS://Topic/_24N1CFW3A).

> #### @icon-info-circle FoxPro uses the 32 bit .NET Core Runtimes
> **Important**: FoxPro is a 32 bit application and so it needs to run the **32 bit version of the .NET Core Runtimes or SDK**. The 32 bit versions are separate and non-default installations, and they are installed in:  
> `c:\Program Files (x86)\dotnet\shared\Microsoft.NETCore.App\`


### Example
There are no major differences between `wwDotnetBridge` and `wwDotnetCoreBridge` so the code you write with either is identical other than the class instantiation.

Here's an example:

```foxpro
CLEAR
SET MEMOWIDTH TO 255

do wwDotNetBridge
LOCAL loBridge as wwDotNetBridge
loBridge = GetwwDotnetCoreBridge()

*** Should echo .NET Core version information
? loBridge.GetDotnetVersion()


*** Load a .NET Core Assembly
IF !loBridge.Loadassembly(".\NetCoreFromFoxPro\NetCoreFromFoxPro\bin\Debug\netcoreapp3.0\NetCoreFromFoxPro.dll")
   ? "Unable to load assembly: " + loBridge.cErrorMsg
   RETURN
ENDIF

loNet = loBridge.CreateInstance("NetCoreFromFoxPro.DotnetSamples")

? "*** Calling Hello World:"
? loNet.HelloWorld("rick")


? "*** Returning a Person"
loPerson = loNet.GetPerson()
? loPerson
? loPerson.Name
? loPerson.Company
? loPerson.Entered
?

? "*** Passing a Person"
loPerson.Company = "East Wind Technologies"
? loNet.SetPerson(loPerson)


*** Create a built-in .NET class and run a method
loHttp = loBridge.CreateInstance("System.Net.WebClient")
loHttp.DownloadFile("https://west-wind.com/files/MarkdownMonsterSetup.exe",;
                    "MarkdownMonsterSetup.exe")


*** Get all the local User Certificates
loStore = loBridge.CreateInstance("System.Security.Cryptography.X509Certificates.X509Store")

? loBridge.cErrorMsg

*** Grab a static Enum value
leReadOnly = loBridge.GetEnumvalue("System.Security.Cryptography.X509Certificates.OpenFlags.ReadOnly")

*** Use the enum value
loStore.Open(leReadOnly)   && 0 - if value is known

*** Returns a .NET Collection of store items
laCertificates = loStore.Certificates

*** Collections don't work over regular COM Interop
*** so use indirect access
lnCount = loBridge.GetProperty(laCertificates,"Count")

*** Loop through Certificates
FOR lnX = 0 TO lnCount -1
	*** Access collection item indirectly using extended syntax
	*** that supports nested objects and array/collection [] brackets
	LOCAL loCertificate as System.Security.Cryptography.X509Certificates.X509Certificate2	
	loCertificate = loBridge.GetProperty(loStore,"Certificates[" + TRANSFORM(lnX) + "]")
			
	IF !ISNULL(loCertificate)
		? loCertificate.FriendlyName
		? loCertificate.SerialNumber
		? loCertificate.GetName()
		*? loBridge.GetPropertyEx(loCertificate,"IssuerName.Name")
	ENDIF
ENDFOR
```