When you create a new Web Connection project, Web Connection automatically generates a server configuration script for that new project. This script can configure IIS for you creating the Virtual Directory, Application Pool and setting up permissions in a single quick operation.

> #### @icon-info-circle IIS Configuration
> Note that [IIS has to be installed and configured](VFPS://Topic/_22F0XKBMQ) and the Web site you deploy has to exist, before you run the configuration script. and any Web sites you want to install have to exist.

The script name generated is named `MyApp_ServerConfig.prg` where `MyApp` is the name of your project. The script is created as FoxPro code that uses Web Connection's built-in server configuration features to configure an IIS installation. Because the script is plain FoxPro code, you can modify the behavior or add additional configuration functionality to meet your needs. The goal is to have a single step configuration script that can set up your application reliably and in a documented repeatable manner.

### Using the Configuration Script
The configuration script is located in the project's `Deploy` folder and can be run directly as a PRG file from within Visual FoxPro (but make sure you run FoxPro using *Run As Administrator*):

```foxpro
DO MyApp_ServerConfig.prg
```

The script is also added to your server's project, compiled in and accessible via a Command Line switch of `CONFIG`. So from an Administrator Windows Command Prompt you can do:

```
MyApp.exe CONFIG
```

to run the configuration script. This makes it easy to deploy your application and then run the configuration script on your server.

Because the script is based on the project's folder structure, the script is self contained and works if the project is moved. The script uses the project's relative paths to identify which folders and files it needs to work with so you can simply run the script out of whatever location the project is installed in.

### Configuration
The script by default makes some basic assumptions about your environment - it assumes it's configuring the default Web site and uses a virtual directory. You can customize the script's operation by modifying the `MyApp.ini` file in the `[ServerConfig]` section:

```ini
[ServerConfig]
Virtual=wwThreads
ScriptMaps=wwt,wc,wcs,wcsx
IISPath=IIS://localhost/w3svc/1/root
```

##### Virtual
The virtual to create/configure on the Web Server. If you want to configure a Web Site root rather than a virtual, leave the value empty.

##### Script Maps
Provide any script maps you want to register. Script maps are the URL file extensions that map requests to your Web Connection server and process classes. 

##### IISPath
This the IIS Metabase path that points at the IIS Web site you want to configure. The default is `IIS://localhost/w3svc/1/root` which is the default Web site. You can change the site configured by changing the number before the root to match the IIS site ID, which you can find in the IIS Management Console if you select the Web site and go to *Advanced Settings* and look at the *ID* value.



### What the default Script Configures
Based on the project's folder structure the script performs the following tasks:

* Creates the IIS Virtual Directory
* Creates a West Wind Web Connection Application Pool (if it doesn't exist)
* Adds Web Site Authentication
* Creates scriptmaps
* Sets permissions on the Web and Admin Folder
* Sets permissions on the temp folder
* Registers your EXE as a COM server


### Example Configuration Script
The following is an example configuration script from the West Wind Message Board sample application. It's unmodified from the original generated code:

```foxpro
************************************************************************
*  Wwthreads_ServerConfig
****************************************
***  Function: Templated Installation routine that can configure the
***            Web Server for you from this file.
***
***            You can modify this script to fit your exact needs
***
***            You can customize operation of this script in 
***            wwthreads.ini in the [ServerConfig] section.
***            
***    Assume: Called from WwthreadsMain.prg startup code
***            with lcAction parameter
***
***            Launch with from the Windows Command Line
***            Wwthreads.exe config
***            or with a specific IIS site/virtual
***            Wwthreads.exe config "IIS://localhost/w3svc/2/root"
***      Pass: lcIISPath  -  IIS Configuration Path (optional)
***                          IIS://localhost/w3svc/1/root
************************************************************************
LPARAMETERS lcIISPath

DO wwUtils	
TRY
	SET CLASSLIB TO WebServer ADDIT
	SET CLASSLIB TO wwXML ADdit
CATCH
ENDTRY

IF !IsAdmin() 
   MESSAGEBOX("Admin privileges are required to configure the Web Server." + CHR(13) +;
              "Please make sure you run this exe using 'Run As Administrator'",;
              48,"Wwthreads Server Configuration")
   RETURN
ENDIF

*** Try to read from Wwthreads.ini [ServerConfig] section
loApi = CREATEOBJECT("wwAPI")
lcIniPath = FULLPATH("Wwthreads.ini")
lcVirtual = loApi.GetProfileString(lcIniPath,"ServerConfig","Virtual")
IF ISNULL(lcVirtual)
  lcVirtual = "wwThreads"
ENDIF
lcScriptMaps = loApi.GetProfileString(lcIniPath,"ServerConfig","ScriptMaps")
IF ISNULLOREMPTY(lcScriptMaps)
  lcScriptMaps = "wc,wcs,wcsx,wwThreads2"  
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
IF FILE("Wwthreads.exe")
   RUN /n4 "Wwthreads.exe" /regserver

   *** Optionally set DCOM permission - only set if needed
   *** requires that DComLaunchPermissions.exe is available
   * DCOMLaunchPermissions("Wwthreads.WwthreadsServer","INTERACTIVE")
   * DCOMLaunchPermissions("Wwthreads.WwthreadsServer","username","password")
ENDIF

WAIT WINDOW Nowait "Configuration completed..."
RETURN
```