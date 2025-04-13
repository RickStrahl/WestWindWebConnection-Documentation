When you create a new Web Connection project, Web Connection automatically creates a configuration file for your application called `<MyApp>_ServerConfig.prg` that contains the most common things you need to configure your application on IIS.

This file gets compiled into your application and can be executed with:

```foxpro
DO MyAppMain with CONFIG
```

or if you compile to an EXE from the command line with:

```txt
MyAppMain.exe CONFIG
```

This program file use some of Web Connection's built-in tools to programmatically:

* Create Virtuals
* Create Scriptmaps
* Set Permissions on directories
* Register a COM server

### An example
If you have an existing application, this file won't exist, but you can easily hook this up to your own application since all the tooling to do server configuration is built into Web Connection.

The following assumes an application name of `MyApp`. The code uses configuration settings in MyApp.config:

```ini
[ServerConfig]
; *** Leave blank for Root Web Sites
Virtual=MyApp
ScriptMaps=wc,wcs,wcsx,myapp
; *** Change Web Server ID for other sites
IISPath=IIS://localhost/w3svc/1/root
```

The settings in the ini file are used to figure what needs to be configured.

```foxpro
************************************************************************
*  MyApp_ServerConfig
****************************************
LPARAMETERS lcIISPath


IF FILE("WCONNECT.APP")
   MessageBox("This feature only works in the full version of Web Connection",48,"MyApp")
   RETURN
ENDIF

DO wwUtils	
SET CLASSLIB TO WebServer ADDIT
SET CLASSLIB TO wwXML ADdit

IF !IsAdmin() 
   MESSAGEBOX("Admin privileges are required to configure the Web Server." + CHR(13) +;
              "Please make sure you run this exe using 'Run As Administrator'",;
              48,"MyApp Server Configuration")
   RETURN
ENDIF

*** Try to read from MyApp.ini [ServerConfig] section
loApi = CREATEOBJECT("wwAPI")
lcIniPath = FULLPATH("MyApp.ini")
lcVirtual = loApi.GetProfileString(lcIniPath,"ServerConfig","Virtual")
IF ISNULL(lcVirtual)
  lcVirtual = "MyApp"
ENDIF
lcScriptMaps = loApi.GetProfileString(lcIniPath,"ServerConfig","ScriptMaps")
IF ISNULLOREMPTY(lcScriptMaps)
  lcScriptMaps = "wc,wcs,wcsx,csvc"  
ENDIF
IF EMPTY(lcIISPath)
  lcIISPath = loApi.GetProfileString(lcIniPath,"ServerConfig","IISPath")
  IF ISNULLOREMPTY(lcIISPath)
     *** Typically this is the root site path
    lcIISPath = "IIS://localhost/w3svc/1/root"
  ENDIF
ENDIF  


*** Other relative configurable settings
lcVirtualPath = LOWER(FULLPATH("..\Web"))
lcScriptPath = lcVirtualPath + "\bin\wc.dll"
lcTempPath = LOWER(FULLPATH(".\temp"))
lcApplicationPool = "WebConnection"
lcServerMode = "IIS7HANDLER"     && "IIS7" (ISAPI) / IISEXPRESS


loWebServer = CREATEOBJECT("wwWebServer")
loWebServer.cServerType = UPPER(lcServerMode)
loWebServer.cApplicationPool = lcApplicationPool
IF !EMPTY(lcIISPath)
   loWebServer.cIISVirtualPath = lcIISPath
ENDIF

WAIT WINDOW NOWAIT "Creating virtual directory " + lcVirtual + "..."
*** Create the virtual directory
IF !loWebServer.CreateVirtual(lcVirtual,lcVirtualPath)   
   WAIT WINDOW TIMEOUT 5 "Couldn't create virtual."
   RETURN
ENDIF


*** Create the Script Maps
lnMaps = ALINES(laMaps,lcScriptMaps,1 + 4,",")
FOR lnx=1 TO lnMaps
    lcMap = laMaps[lnX]
    WAIT WINDOW NOWAIT "Creating Scriptmap " + lcMap + "..."
	  llResult = loWebServer.CreateScriptMap(lcMap, lcScriptPath)	    
    IF !llResult
       WAIT WINDOW TIMEOUT 2 "Failed to create scriptmap " + lcMap
    ENDIF
ENDFOR


WAIT WINDOW NOWAIT "Setting folder permissions..."

lcAnonymousUserName = ""
loVirtual = GETOBJECT(lcIISPath)
lcAnonymousUserName = loVirtual.AnonymousUserName
loVirtual = .NULL.

*** Set access on the Web directory -  should match Application Pool identity
SetAcl(lcVirtualPath,"Administrators","F",.T.)
SetAcl(lcVirtualPath,"Interactive","F",.T.)
SetAcl(lcVirtualPath,"NETWORKSERVICE","F",.T.)
* SetAcl(lcVirtualPath,"OtherUser","F",.T.,"username","password")

*** IUSR Anonymous Access
IF !EMPTY(lcAnonymousUserName)
    llResult = SetAcl(lcVirtualPath,lcAnonymousUserName,"R",.T.)

    *** No unauthorized access to admin folder
    lcAdminPath = ADDBS(lcVirtualPath) + "Admin"
    IF DIRECTORY(lcAdminPath)
   	   llResult = SetAcl(lcAdminPath,lcAnonymousUserName,"N",.t.)
    ENDIF
ENDIF

*** Set access on the Temp directory - should match Application Pool Identity
SetAcl(lcTempPath,"Administrators","F",.T.)
SetAcl(lcVirtualPath,"Interactive","F",.T.)
SetAcl(lcTempPath,"NETWORK SERVICE","F",.T.)
* SetAcl(lcTempPath,"OtherUser","F",.T.,"username","password")

*** COM Server Registration
IF FILE("MyApp.exe")
   RUN /n4 "MyApp.exe" /regserver

   *** Optionally set DCOM permission - only set if needed
   *** requires that DComLaunchPermissions.exe is available
   * DCOMLaunchPermissions("MyApp.MyAppServer","INTERACTIVE")
   * DCOMLaunchPermissions("MyApp.MyAppServer","username","password")
ENDIF

WAIT WINDOW Nowait "Configuration completed..."
RETURN
```

This script is not generic obviously as it's tailored to a specific application (MyApp), but it can be customized quite easily in a few places for most configurations. 

You might want to add or remove some script maps (like ASPX which was used for this particular project) or you might need additional settings applied say to the virtual directory.  

The beauty of this sort of script is that once you have your configuration set up it's very easy to run it for a first time config or even to reconfigure if something should get accidentally removed. 

If you want to deploy this you can either choose to build an EXE out of this small program by adding to a project and compiling into an EXE, or maybe even easier by adding it to your main server EXE itself with code like the following in the startup program (like wcDemoMain.prg):

```foxpro
************************************************************************
*FUNCTION MyAppMain
******************************
LPARAMETER lcAction, lvParm1, lvParm2

#INCLUDE WCONNECT.H
RELEASE goWCServer

*** Load the Web Connection class libraries
IF FILE("WCONNECT.APP")
   DO ("WCONNECT.APP")
ELSE
      DO WCONNECT
ENDIF

*** CONFIG HANDLING HOOKED UP HERE ***
IF VARTYPE(lcAction) = "C" AND StartsWith(LOWER(lcAction),"config")
       do Test2_ServerConfig.prg with lvParm1
    RETURN 
ENDIF

*** Load the server - wc3DemoServer class below
goWCServer = CREATE("MyAppServer")
...
READ EVENTS
...
```

With this code the configuration script is now part of the server and you can simply do:

```txt
MyApp.exe config
```

or from the FoXpro command prompt:

```foxpro
do MyAppMain with "config"
```


To run the configuration code. Simple and self contained and makes the configuration script available along with your EXE always.

### File Copying
One thing that helps is to create your deployment structure something like this:

```txt
AppRoot
	ApplicationFiles	(EXE,Ini files, Web Connection Tools/html/Scripts/Template folders)
	Web       		(the Web Virtual directory where WCSX etc. scripts and templates live)	
	Data			(Optional data directory if you're using VFP data)
```

Note that in this scenario the web folder is NOT underneath the actual Web root (ie. c:\inetpub\wwwroot), but this works perfectly fine as long as the path is configured properly and permissions are set appropriately specifically for the IIS anonymous user (ie. IIS_YourMachine) to have read/execute access. The configuration script above handles this automatically by looking up the Anonymous account and setting permissions on the Web folder for this account.

For a more elaborate example of a front end you can take a look at the wwAppWizard class (especially the Configure Server method) and the ConfigureServer class in wcSetup.vcx. The source code for these classes is provided and you can build your own wizards based on them if you choose. Or you can build a much simpler form interface to customize with just the 4 or 5 configurable options to present to the user. In your own applications most options other than the install path are probably fixed so the user interface can be pretty simple for a front end.

Note that source code for the Setup, New Project and Configuration Wizards is also available - you can customize those Wizards with custom logos and customized code. The actual configuration code resides in wwAppWizard and you can override any of the individual methods as you see fit to customize setup behaviors.