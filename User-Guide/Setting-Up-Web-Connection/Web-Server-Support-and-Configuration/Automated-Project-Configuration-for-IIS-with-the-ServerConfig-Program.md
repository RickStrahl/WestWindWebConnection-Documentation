When you create a new Web Connection project, the project automatically generates a `<yourApp>_ServerConfig.prg` file, which is a plain FoxPro program that configures the local project folder for use with IIS. 

You can also run this same operation from your compiled Web Connection executable with `<yourApp>.exe CONFIG`.

The program configures an IIS Web site for Web Connection by:

* Creating or updating an Application Pool
* Configuring the Web Root or virtual
* Adding Authentication features
* Setting folder permissions

These are all the required Web site configuration steps outlined in the [IIS configuration topic](VFPS://Topic/_22F0XKBMQ), but automated and configured specifically for your project.

> #### @icon-warning Web Site to configure must exist
> Before you run the ServerConfig program make sure that the Web Site that you want to install your Application into exists. This can be the default Web site (which always exists), but if you want to use a new IIS Web site, make sure you create the site first and know what the Web Site ID (an integer) is.

*In the following examples I'm going to assume you use a project called `WebDemo` which produces  `WebDemoMain.prg` and `WebDemo_ServerConfig.prg` and `WebDemo.exe`. When I use the `WebDemo` name assume you need to replace this with your project's name.*

### The ServerConfig Program
The generated file is in the format of `<yourApp>_ServerConfig.prg`, so for this project you would do:

```foxpro
DO WebDemo_ServerConfig
```

> #### @icon-warning You need to run as an Administrator to configure IIS
> The program will prompt you to restart your IDE if you're not running as an Administrator.

### YourProject.exe CONFIG Command Line
That same ServerConfig program also gets compiled into your Web Connection application executable, so you can run this code as part of production server installation without requiring a FoxPro IDE install to run the PRG.

Once you have a compiled EXE, you can do the following out of your project's `Deploy` folder where the EXE lives:

```ps
WebDemo.exe CONFIG
```

Again, **you have to run as an Administrator** for this to work.

### Configuration
When the project is created the ServerConfig program creates default settings for the project. For most scenarios the default settings work perfectly fine, but you can tweak the configuration settings that the program uses via settings in your `WebDemo.ini` (yourApp.ini) file.

```ini
[ServerConfig]
IISPath=IIS://localhost/w3svc/1/root
Virtual=webdemo
ScriptMaps=wp,wc,wcs
```

#### IISPath
This is the IIS Configuration path (an ADSI path) to the Web site under which the this application is installed. The default path:

```ini
IISPath=IIS://localhost/w3svc/1/root
```

is the root of the default Web site. The number before `/root` (`1` in this case) determines the **Web Site Id** which you can find in the IIS Manager's list of Web sites. Each Web site has an integer Id, and you can change the ID to the **existing** Web Site you want to install to.

> #### @icon-warning IIS Web Site has to exist
> **Web Connection does not create new top level IIS Web sites**, so you have to create them manually in the IIS Manager (or other admin tools). A Web site is the top level node below the Machine in the IIS Manager, and corresponds to the integer Web site ID mentioned above. You likely also have to configure a local domain name for the site in your `HOSTS` file - or alternately use the site on a non-80/443 port in order for the site to be accessible. 
>
> The Web Connection Console can create a **new root or virtual *Applications* on an existing Web site** and configures this application, but the top level Web Site has to exist first.

#### Virtual
Lets you specify that the application should be created as a Virtual underneath the Web root. Traditionally Web Connection applications were always created as Virtuals but that is no longer necessary. 

The Virtual value can either be the name of a Virtual (like `webdemo`) or be **left blank** for running under the root.

#### IIS Development vs. Production
If you use full IIS - rather than IIS Express or the Web Connection Web Server -  for local development you are likely to be using a virtual directory, but when you deploy on a production server you are very likely to run under the Web root.

So what you'd do is have the **local install use a virtual folder value** and have the **production install use a blank value** for the **Virtual** key.

#### ScriptMaps
Scriptmaps configures all the scriptmaps for your application and explicitly adds them to `web.config` file. This list should include your application specific script map(s) as well as `wc` and `wcs` which are internal scriptmaps used to access administration requests and run generic Web Connection scripts.


### ServerConfig is Customizable: It's just FoxPro Code
The server configuration is a FoxPro program file that you can customize. The program uses Web Connection's server configuration classes to configure the Web Server using the minimum settings that are required to configure a Web Connection Web site.

But because it's a FoxPro program you can easily modify or add additional configuration steps to this script. 

It's an ideal location for adding additional automated Production Site configuration steps that your application might need in order to run on a server. You can add additional permissions, perhaps create additional folders, run a follow up executable that creates application lookup data or default data sets etc.

For reference here is the configuration file generated for the `WebDemo` project.

```foxpro
************************************************************************
*  WebDemo_ServerConfig
****************************************
***  Function: Templated Installation program that can configure the
***            Web Server automatically.
***
***            You can modify this script to fit your exact needs
***            by modifying existing behavior or adding additional
***            configuration steps.
***
***            The base script is driven by the [ServerConfig] section in
***            WebDemo.ini and you can customize operation there.
***
***            [ServerConfig]
***            Virtual=WebDemo            
***            ScriptMaps=wc,wcs,wcsx,tp    
***            IISPath=IIS://localhost/w3svc/1/root
***
***            For root Web sites leave Virtual blank. To pick a specific
***            Web site to install to, use the IIS Site Id in the IISPath
***            the number of which you can look up in IIS Service Manager
***            and the Site list Tab.
***
***    Assume: Called from WebDemoMain.prg startup code
***            with lcAction parameter:
***            DO WebDemoMain with "CONFIG"
***
***            Or launch your Exe from the Windows Command Line:
***            WebDemo.exe config
***
***            or with a specific IIS site/virtual
***            WebDemo.exe config "IIS://localhost/w3svc/12/root"
***
***            This program assumes you're running out of the Web Connection
***            EXE folder and assumes the Web Folder lives relative at `..\Web`.
***
***      Pass: lcIISPath  -  IIS Configuration Path (optional)
***                          IIS://localhost/w3svc/1/root
************************************************************************
LPARAMETERS lcIISPath
LOCAL lcAppName, lcDefaultVirtual, lcScriptMap

lcAppName = "WebDemo"
lcDefaultVirtual = "WebDemo"
lcScriptMap = "tp"

IF FILE("WCONNECT.APP")
   MessageBox("This feature only works in the full version of Web Connection",48,lcAppName)
   RETURN
ENDIF

DO wwUtils	
SET PROCEDURE TO wwWebServer ADDITIVE
SET PROCEDURE TO wwXML ADDITIVE

IF !IsAdmin() 
   MESSAGEBOX("Admin privileges are required to configure the IIS Web Server." + CHR(13) +;
              "Please make sure you run this exe using 'Run As Administrator'",;
              48, lcAppName + " Server Configuration")
   RETURN
ENDIF

*** Try to read from WebDemo.ini [ServerConfig] section
loApi = CREATEOBJECT("wwAPI")
lcIniPath = FULLPATH(lcAppName + ".ini")
lcVirtual = loApi.GetProfileString(lcIniPath,"ServerConfig","Virtual")
IF ISNULL(lcVirtual)
  lcVirtual = lcDefaultVirtual
ENDIF
lcScriptMaps = loApi.GetProfileString(lcIniPath,"ServerConfig","ScriptMaps")
IF ISNULLOREMPTY(lcScriptMaps)
  lcScriptMaps = "wc,wcs,wcsx," + lcScriptMap  
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
lcApplicationPool = "West Wind Web Connection"
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
* SetAcl(lcVirtualPath,"OtherUser","F",.T.)

*** Remove IUSR from Admin folder
SetAcl(ADDBS(lcVirtualPath) + "Admin","IUSR","N",.T.)


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
SetAcl(lcTempPath,"Interactive","F",.T.)
SetAcl(lcTempPath,"NETWORKSERVICE","F",.T.)
* SetAcl(lcTempPath,"OtherUser","F",.T.,"username","password")

*** COM Server Registration
lcExe = lcAppName + ".exe"
IF FILE(lcExe)
   RUN /n4 &lcExe /regserver

   *** Optionally set DCOM permission - only set if needed!
   *** requires that DComLaunchPermissions.exe is available in .\tools\
   * DCOMLaunchPermissions(lcAppName + "." + lcAppName + "Server","INTERACTIVE")
   * DCOMLaunchPermissions(lcAppName + "." + lcAppName + "Server","username","password")
ENDIF

WAIT WINDOW Nowait "Configuration completed..."
RETURN
```

### @icon-info-circle Create Latest Version of ServerConfig
> If you have a real old version of the ServerConfig file, you can update it by creating a temporary, new project using the Web Connection Managment Console. You can pick up the newly generated `<appName>_ServerConfig.prg` file and copy and rename it for your own project. The only values that project specific are the variables at the top of the code - adjust those and you're good to go.

Note that this uses a number of Web Connection configuration functions. For reference, you can search for these functions and what they using the search index in this documentation.