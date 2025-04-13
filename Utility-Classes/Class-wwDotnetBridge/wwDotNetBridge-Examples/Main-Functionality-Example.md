The following example demonstrates a variety of the features of the wwDotNetBridge class:

```foxpro
CLEAR
DO wwDotNetBridge
LOCAL loBridge as wwDotNetBridge

loBridge = CREATEOBJECT("wwDotNetBridge")

*** Load an assembly 
? loBridge.LoadAssembly("System")  && 'System' is a special name and not really necessary - loaded by default
? loBridge.cErrorMsg

*** Note full load is syntax by 'fully qualified assembly name':
* loBridge.LoadAssembly("System, Version=2.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089")

*** Create an instance of a .NET class
loMsg = loBridge.CreateInstance("System.Net.Mail.MailMessage")
? loBridge.cErrorMsg
*!*	? loMsg
? loMsg.ToString()

*** Load Static Method
? loBridge.LoadAssembly("System.Windows.Forms")  
? loBridge.InvokeStaticMethod("System.Windows.Forms.MessageBox","Show","Hello World","Title is it")
? loBridge.cErrorMsg

*** Get a Static value (System.Environment.CurrentDirectory)
? loBridge.GetStaticProperty("System.Environment","CurrentDirectory")

*** Get an Enum value (basically a static) 
? loBridge.GetEnumValue("System.Windows.Forms.MessageBoxDefaultButton","Button3")

*** Load one of my own assemblies
? loBridge.LoadAssembly("C:\projects2005\WebLogBusiness\bin\debug\weblogbusiness.dll")
? loBridge.cErrorMsg

*** Retrieve a static property which is an object
loConfig = loBridge.GetStaticProperty("Westwind.WebLog.App","Configuration")
? loConfig.ApplicationName

*** I could also instantiate the config object directly
loConfig = loBridge.CreateInstance("Westwind.WebLog.AppConfiguration")
loConfig.Initialize()


? loBridge.cErrorMsg

*** Object now in VFP - just access properties and methods
? loConfig.WebLogTitle
? loConfig.MailServerName
? loConfig.WebLogTile = "Some OtherValue" 

*** Set and read a static property on a custom static object
? loBridge.SetStaticProperty("Westwind.WebLog.App","ApplicationOfflineMessage","NEW VALUE")
? loBridge.GetStaticProperty("Westwind.WebLog.App","ApplicationOfflineMessage")
```