The application INI file contains [server settings](VFPS://Topic/_5RL1A4UEV) that the Web Connection server and your individual process modules need for operation. It can also contain custom settings that you want to store for your own application specific purposes.

The server settings are required while the process module settings are optional and can be defined by you as you need them. By default Web Connection manages the INI file settings through a custom implementation of the [wwServerConfig class](vfps://Topic/Class%20wwServerConfig) (defined in wwServer.prg and subclasses from the [wwConfig](vfps://Topic/Class%20wwConfig) class) which dynamically manages the settings via an object that persists its properties into the INI file.

The application INI file has the same name as the project. So the demo application executable (and project file) is `wcDemo.exe` and the ini file is `wcDemo.ini`. The Ini file looks like this (more info on [server settings](VFPS://Topic/_5RL1A4UEV))

```ini
[Main]
Tempfilepath=.\temp\
Template=WC_
Scriptmode=2

Debugmode=On
LiveReloadEnabled=Off

Logtofile=On
Saverequestfiles=On
Showrequestdata=Off
Showserverform=On  
Showstatus=On 
Showdesktopform=Off

Comreleaseurl=http://localhost:5200/ReleaseComServers.wc
HomeUrl=http://localhost:5200
CodeEditorCommandline=C:\Program Files (x86)\Microsoft Visual Studio\2019\Enterprise\Common7\IDE\devenv.exe ""
Markdownpagesanitized=Off

Memusage=8176
Timerinterval=200
Unattendedcommode=Off

Adminmailserver=localhost
Adminemail=rstrahl@west-wind.com
Adminmailusername=
AdminMailpassword=
Adminmailusessl=Off 
Adminsenderroremail=Off

Sqlconnectstring=database=WestWindAdmin;
Systemfilesdatapath=
Postscriptpdfprinterdriver=Microsoft Print to PDF


[ServerConfig]
Virtual=wconnect
ScriptMaps=wc,wcs,wcsx,wwd,wst,md,blog
IISPath=IIS://localhost/w3svc/1/root


[wwdemo]
datapath=d:\wconnect\data
htmlpagepath=\wconnect\web
Virtualpath=/wconnect/
```

The `[Main]` section contains the main Web Connection Server settings.  The `[ServerConfig]` section is used for IIS configuration of the application - those settings are used by the auto-config to setup an application to run under IIS. 

Any other sections like `[wwdemo]` belong to Process classes - they are process class configuration. The default settings there for HtmlPagePath and VirtualPath are not really used any more as those values are now provided by the Web server, but they are there as fallbacks in case the Web Server values are not provided (for example Apache doesn't provide an easy way to return the physical path of the Web site). These are used internally. 

However you can easily **add properties to the appropriate** `Server.owwDemoConfiguration` class in this case and those settings are then configurable via the ini file as well. Simply add a property and you can then configure via the ini file. This is a great way to store process specific configuration in reusable way.


### The wwConfig Class
All of the INI sections above are driven by `wwConfig` classes. The hierachy is:

```foxpro
DEFINE CLASS oMyAppConfiguration
   
*** Server settings
cTempFilePath = ""
lDebugMode = .F.
lLiveReloadEnabled= .F.


*** Process class instances (1 or more)
owwDemo = .NULL.

FUNCTION Init()

CREATEOBJECT("wwDemoConfig")

ENDFUNC

ENDDEFINE

DEFINE CLASS wwDemoConfig as RELATION

cHTMLPagePath = "d:\westwind\wconnect\"
cDATAPath = "d:\wwapps\wc3\wwDemo\"

ENDDEFINE
```

The configuration objects are exposed on the `Server` object for the server configuration and on the `Process` object for process configuration. 

It looks something like this:

```foxpro
*** Server settings
Server.oConfig.lDebugMode

*** Process class settings
lcPath = Server.oConfig.owwDemo.cHtmlPagePath

*** Process class settings in a Process class
lcVirtual = Process.oConfig.cVirtual
```

The process configuration uses this scheme: `Server.o<processClassName>` by default. So `wwDemo` process class is exposed as `Server.oConfig.owwDemo`. Inside of a process class there's a simpler way that exposes the same process specific as `Process.oConfig`.

These objects are very useful and often underappreciated because they are not really obvious. But if you need to set configurable configuration values like paths and common operational settings, these COnfig classes are a great way to do this.

> **Important:** All properties you create should be created with a type prefix like `cServerPort`, `cDataPath`, `nSeconds`, `lIsDevelopment`. The prefix is dropped when the value is written to the INI file. If you omit the prefix you'll run into truncated values in the INI file - it'll still work, but it sure will look funny.