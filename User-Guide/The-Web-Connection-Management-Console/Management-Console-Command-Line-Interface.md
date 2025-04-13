The Management Console can also activate a number of options via commandline so you can by pass the main menu and perform various tasks directly through the management interface.

The syntax is the key word, plus in some cases additional parameters you can pass.

From within Visual FoxPro:
 
```foxpro
DO CONSOLE WITH "SQLCONFIG"
DO CONSOLE WITH "VIRTUAL","wconnect","D:\web\wconnect"
DO CONSOLE WITH "AUTOLOGON","rick","superseekrit"
DO CONSOLE WITH "UPDATEPROJECTRESOURCES","c:\webConnectionProjects\MyProject",modules,dlls,libs,scripts,css"
```

From the Windows Command Prompt:

```dos
CONSOLE "SQLCONFIG"
CONSOLE "VIRTUAL" "wconnect" "D:\web\wconnect"
CONSOLE "AUTOLOGON" "rick" "superseekrit"
CONSOLE "UPDATEPROJECTRESOURCES"
```

The following useful utility functions are available through the console:

### SETUP
Starts the new [Web Connection Setup](VFPS://Topic/_S7R0OBMM1) routine.

### NEWPROJECT
Starts the [new project Wizard](VFPS://Topic/_S7R0OXD9T).

### NEWPROCESS
Starts the [New Process Wizard](VFPS://Topic/_S7R13T6G7)

### UPDATEPROJECTRESOURES
When Web Connection is updated to a new version, there are number of resources that need to be updated in your project to match the new files provided by Web Connection. This helper lets you easily update select components to the latest shipped versions using automated copy routines. Note this tool is used **locally** to update resources - you're still responsible for updating your server installations from your updated local install.

This update routine updates your local project to the latest and greatest, and you then take that updated project and deploy it to your server.

> The update will work best if the project **is not running** and files are not in use. Please shut down the running project, and perhaps restart the Web service to make sure there are no locked files that won't update.

#### Parameters

**UpdatePath**

*"c:\webconnectionprojects\MyProject"* or *""*

The path where the project to update lives. The path should be the **root path of a standard Web Connection project** with sub-folders for `deploy` and `web`. If you're using the default location it'd be like `\WebConnectionProjects\MyProject` for example. You can pass `""` for `Updatepath` to pick the folder interactively.

**UpdateMode** 

*"modules,dlls,libs,scripts,css"*

Determines what components should be updated in the target project. The following options are available to update and by default they all get updated:

* **modules**: The Web Modules - webconnectionmodule.dll, wc.dll, the Apache module, also WebConnectionWebServer
* **dlls**: The Web Connection Support DLLs - wwdotnetbridge, wwipstuff, newtonsoft.json, markdig etc.
* **libs**: Third party Script Libraries: jquery, bootstrap, fontawesome etc.
* **views**: The Web Connection stock Page templates in `\web\views`
* **scripts**: Web Connection Script Libraries -`ww.jquery.js`
* **css**: Web Connection CSS - `css\application.css`

**Examples:**

```foxpro
# Using default list of update operations you get to pick the path
DO CONSOLE WITH "UPDATEPROJECTRESOURCES"

# Custom list of items to update operations - leaving out css
DO CONSOLE WITH "UPDATEPROJECTRESOURCES", ;
        "c:\WebConnectionProjects\MyProject", ;
        "modules,dlls,libs,scripts" 
```

### SCRIPTMAP
Allows you to create a scriptmap for the appropriate Web Server. Run without additional parameters to get a list of parameters and options or with "UI" to get prompted for options.

```ps
CONSOLE "SCRIPTMAP" "wwd,wc,blog,wwt"
        "c:\webconnectionprojects\wwdemo\web" 
```

> @icon-info-circle You can also look at the comments in `\tools\scriptmap.prg` which you can also run and integrate into your own code.

### VIRTUAL
Creates a virtual directory. Run without additional parameters to get a list of parameters and options or with "UI" to get prompted for options.

```ps
CONSOLE "VIRTUAL" "wwdemo" "c:\webconnectionprojects\wwdemo\web"
```

> @icon-imulti-nfo-circle You can also look at the comments in `\tools\virtual.prg` which you can also run and integrate into your own code.

### IISEXPRESS
Launches IIS at a specific path and port.

```ps
CONSOLE IISEXPRESS "c:\webconnectionprojects\wwdemo\web" 7001 
```

You can also run this command from FoxPro IDE from a project

```foxpro
DO CONSOLE WITH "IISEXPRESS",LOWER(FULLPATH(".\Web")),7001

DO CONSOLE WITH "IISEXPRESS",LOWER(FULLPATH(".\Web")),7001,"/wconnect","NONAVIGATE"
``` 

an optional last parameter of "NONAVIGATE" can be passed to specify that this command should not navigate to the site root.

### WEBCONNECTIONWEBSERVER
Launches the .NET Core Web Server in a specific path.

```ps
# Launches Web Connection Web Server from ..\Web folder on default port (5200)
CONSOLE WEBCONNECTIONWEBSERVER 

# Launches in folder on default port  (localhost:5200)
CONSOLE WEBCONNECTIONWEBSERVER "c:\webconnectionprojects\wwdemo\web" 
```

You can also run this command from FoxPro IDE from a project

```foxpro
* Launches on a specific port 
DO CONSOLE WITH "WEBCONNECTIONWEBSERVER",LOWER(FULLPATH("..\Web")),5300
```
### DOTNETINFO
Provides information on .NET version installed on the system as well as wwDotnetBridge version and location information. Running this command also provides an easy way to check and ensure that wwDotnetBridge is working properly for this machine.

```
CONSOLE "DOTNETINFO"
```

The output generated is displayed on a message box and looks like this:

```
wwDotnetBridge Version   : 7.7.0.0
wwDotnetBridge Location  : C:\WEBCONNECTION\FOX\WWDOTNETBRIDGE.dll
.NET Version (official)  : 4.0.30319.42000
.NET Version (simplified): 4.8
.NET Version (Release)   : 528040
Windows Version          : 6.2.9200.0 - Release: 1903
```


### DCOMNCONFIG
Combines the DCOMIMPERSONATION and DCOMPERMISSION into a single command.

```ps
CONSOLE "DCOMCONFIG","<ProgId>","<UserName>","<password>"
```

Examples:

```ps
CONSOLE "DCOMCONFIG" "wcdemo.wcdemoserver" "rick","seekrit"
CONSOLE "DCOMCONFIG" "wcdemo.wcdemoserver" "NETWORK SERVICE"
CONSOLE "DCOMCONFIG" "wcdemo.wcdemoserver" "SYSTEM"
```

We suggest you only run of these.  If you do run multiple commands make sure the one you want to actually use for impersonation is the last one you run since that's what gets assigned to the DCOM Impersonation setting.

Note all DCOM permissions are set in the 32 bit DCOM registry even on a 64 bit system, since FoxPro COM servers are 32 bit.

### DCOMIMPERSONATION
Sets the DCOM impersonation for a COM server, which is the account the COM server will run under when launched.

```ps
CONSOLE "DCOMIMPERSONATION","<ProgId>","<UserName>","<password>"
```

Note that the username is not provided for accounts like Interactive or SYSTEM. If username is omitted Interactive is used.

> **Note**
> This option requires that the DCOMPERMISSIONS.EXE file from the Tools directory is either in the current path or the TOOLS directory.</blockquote></small>

### DCOMPERMISSION
Sets a single DCOM permission for a COM server for a single account you specify. 

```ps
CONSOLE "DCOMPERMISSION" "<ProgId>" "<Username>"
```

Username can be either a System username (SYSTEM,IUSR_RASNOTEBOOK), Group (Administrators) or normal username.

> **Note:
> This option requires that the DCOMPERMISSIONS.EXE file from the Tools directory</blockquote></small>

### DISABLELOOPBACKCHECK
This command lets you bypass Windows server local loopback restrictions that disallow authenticating in IIS using Windows Authentication. This can affect Web Connection Admin pages which rely on Windows Authentication to authenticate access to the admin pages.

Running the command without a parameter enables local Windows authentication. Running with a parameter of `off` removes the settings in the registry.

```ps
*** Disable
CONSOLE "DISABLELOOPBACKCHECK"

*** Remove Registry entry (Enable)
CONSOLE "DISABLELOOPBACKCHECK" "Off"
```

### INSTALLPRINTER
Allows you to install one of Windows' default printer drivers automatically. You can use this feature to easily install the "Apple Color LW 12/660 PS" Postscript driver that can be used with the wwDistiller and wwGhostscript classes to generate PDF output. This option will default to the above printer and allow you to type in a known Windows printer name.

You can optionally pass in the name of a driver:

```ps
CONSOLE.EXE "INSTALLPRINTER" "Microsoft Print to PDF"
```

### GOURL
Runs any URL or Windows file association and displays the content in the Web browser.

The following options access the various user interface wizards and helpers directly.

### SQLCONFIG
Starts the SQL Configuration Wizard