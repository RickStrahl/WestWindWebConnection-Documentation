The wwServer class serves as the entry point of your application. It handles access via the various mechanisms of File Based, COM and ASP. It sets up the Request object for the request and knows how to deal with the outgoing HTTP stream.

To create a new application you create a new instance of the wwServer class.  When running COM or ASP the server itself is the startup code since the COM object is directly invoked. When running File Based however a small loader program must first load the server into memory by creating the object and then waiting for incoming requests:

```foxpro
#INCLUDE WCONNECT.H 

PUBLIC glExitServer, goWCServer

*** Load the Web Connection class libraries
DO WCONNECT

*** Load the server - wc3DemoServer class below
goWCServer = CREATE("wcDemoServer")
                    
*** Make the server live - Show puts the server online and in polling mode
READ EVENTS
   
RETURN
```

This instantiates the server in File Based mode, and waits for incoming requests which are checked in a timer event. When a request comes in it's processed and the output returned. The server goes idle again then simply waiting at the READ EVENTS. The file based stub is the startup required for a file server since it can't 'stand on its own'.

Using COM the server needs no startup code since the server is called directly. Instead of a form's timer firing the ProcessHit() method is called directly and off the server goes. When the COM Server is done it returns to idle status in the pool waiting for the next request.

Web Connection can automatically manage figuring out how to run the server based on which mode the server is set up for.

### The wwServer class implentation
The wwServer class handles all of this conversion. Each application you build has to subclass the wwServer class with your own custom version. This is required so you can configure the application for your environment and your application classes. Actually the New Project Wizard creates a wwServer subclass for you and the generated code looks something like this (plus a few customizations):

```foxpro
#INCLUDE WCONNECT.H

**************************************************************
DEFINE CLASS wcDemoServer AS WWC_SERVER OLEPUBLIC
*************************************************************

*** Add any custom properties here

************************************************************************
* wcDemoServer :: OnInit
************************
PROTECTED FUNCTION OnInit

*** Location of the startup INI file 
THIS.cAppIniFile = addbs(THIS.cAppStartPath) + "wcDemo.ini"
THIS.cAppName = "Web Connection Demo"

*** Main Config for this class. Class below in this PRG file
THIS.oConfig = CREATE("wcDemoConfig")
THIS.oConfig.cFileName = THIS.cAppIniFile

SET CENTURY ON

ENDFUNC
* SetServerEnvironment


************************************************************************
* wcDemoServer :: OnLoad
************************
PROTECTED FUNCTION OnLoad

*** Any settings you want to make to the server
IF THIS.lShowServerForm
  THIS.oServerForm.Caption =THIS.cServerId + " - Web Connection " + WWVERSION 
ENDIF  

*** Add any application paths that I might to access
*** Remeber these may not be relative in COM object
*** hence the full path!
DO PATH WITH THIS.cAppStartpath && Required when running as InProc COM object
DO PATH WITH THIS.cAppStartPath + "CLASSES\"
DO PATH WITH THIS.cAppStartPath +"WWDEMO\"
DO PATH WITH THIS.cAppStartPath + "WEBCONTROLS\"


*** Add any data paths - SET DEFAULT has already occurred so this is safe!
DO PATH WITH THIS.cAppStartPath + "WWTHREADS\"
SET PROCEDURE TO wwtClasses ADDITIVE
SET PROCEDURE TO wwtList ADDITIVE

SET CLASSLIB TO wwStore ADDITIVE
ENDFUNC
* SetServerProperties

************************************************************************
* wcDemoServer :: Process
*************************
PROTECTED FUNCTION Process
LOCAL lcParameter, lcExtension, lcPhysicalPath

*** Retrieve first parameter
lcParameter=UPPER(THIS.oRequest.Querystring(1))  

*** Set up project types and call external processing programs:
DO CASE
     CASE lcParameter == "WWTHREADS"
	     DO wwThreads with THIS

	  CASE lcParameter == "WWDEMO"
	     DO wwDemo with THIS

	  *** HTTP Client Demos
	  CASE lcParameter == "HTTP"
	      DO HTTP with THIS      

	  CASE lcParameter =="WWSTORE"
	     DO WWSTORE WITH THIS

      *** SUB APPLETS ADDED ABOVE - DO NOT MOVE THIS LINE ***

	  CASE lcParameter == "WWMAINT"
	      DO wwMaint with  THIS
  OTHERWISE
     *** Check for Script Mapped files for: .WC, .WCS, .FXP
     lcPhysicalPath=THIS.oRequest.GetPhysicalPath()
     lcExtension = Upper(JustExt(lcPhysicalPath))

     DO CASE
     CASE lcExtension == "WWS"
        DO wwStore with THIS

     *** ADD SCRIPTMAP EXTENSIONS ABOVE - DO NOT MOVE THIS LINE ***
     CASE lcExtension = "WWT"
        DO wwThreads with THIS
        
     *** Web Connection Demo handling
     CASE lcExtension = "WWD" 
        DO wwDemo with THIS

     CASE lcExtension = "WC" OR lcExtension == "FXP"
        DO wwScriptMaps with THIS

     OTHERWISE
         *** Error - No handler available. Create custom 
	     Response=CREATE([WWC_RESPONSESTRING])
	     Response.StandardPage("Unhandled Request",;
	                       "The server is not setup to handle this type of Request: "+lcParameter)

	     IF THIS.oConfig.lAdminSendErrorEmail
	       LOCAL loIP
           loIP = CREATE("wwIPStuff")
		   loIP.cMailServer = THIS.oConfig.cAdminMailServer
		   loIP.cSenderEmail = THIS.oConfig.cAdminEmail
		   loIP.cRecipient = THIS.oConfig.cAdminEmail
		   loIP.cSubject = "Web Connection Error Message - Unhandled request"
		   loIP.cMessage = CRLF + ;
	           "The request Query String is: " +THIS.oRequest.QueryString() + CR +;
	           "              DLL or Script: " +THIS.oRequest.ServerVariables("Executable Path") + CR+;
	           "                Server Name: " + THIS.oRequest.GetServerName() 
   
           *** Send and immediately return
           loIP.SendMailAsync()
         ENDIF

	     IF THIS.lCOMObject
	         *** Simply assign to output property
	         THIS.cOutput=Response.GetOutput()
	     ELSE
	         *** FileBased - must output to file
	         File2Var(THIS.oRequest.GetOutputFile(),Response.GetOutput())
	     ENDIF
	 ENDCASE
   
ENDCASE

RETURN
* EOF wc3DemoServer::Process

ENDDEFINE
* EOC wcDemoServer


DEFINE CLASS wcDemoConfig as wwServerConfig

owwStore = .NULL.

FUNCTION Init
THIS.owwStore = CREATE("wwStoreConfig")
ENDFUNC

ENDDEFINE

DEFINE CLASS wwStoreConfig as RELATION

cHTMLPagePath = "d:\westwind\wwStore\"
cDATAPath = ".\wwStore\"
cXMLDocRoot = "wwstore"
cAdminUser = "Any"

ENDDEFINE
```

Most of this code is boilerplate, which means you can simply cut and paste it for each full application server. The New Project Wizard will set up a default configuration for your server with the appropriate parts filled in. The project should be ready to run when the Wizard completes.

The key things that you will change for each fully self contained server are:

### OnInit
This method fires very early in the creation process of the server and is used to configure server settings that are crucial to how the server loads itself. Here you should override server properties and file locations like the startup INI file if you need to override it. This is a key feature actually - reading the application's INI file determines most of the startup values the server uses. The startup INI file is read after this method completes. You can override the settings loaded if desired in OnLoad().

### OnLoad
OnLoad() fires after the server has initialized itself with the configuration settings from the INI file and internally has set its environment. When OnLoad() fires the server is basically ready to receive requests. This is your hook to set application specific settings in the server environment and override any Server properties that were loaded by default from the INI file or otherwise are set on the server.

The most common things to do in OnLoad() is to load class libraries and add additional paths to the FoxPro path so data and support files can be found.

### Process
The Process method is called on every hit of the server and is responsible for routing requests to the appropriate application class. This method handles routing by looking at the URL. It looks for routing info by default in two locations:
* The first positional parameter in a ~ separated list
* The 'page' extension
In the example above a first parameter of wwDemo and .wwd extension would route to the same class so the following URLs are actually doing the same thing:
    * /wconnect/wc.dll?wwDemo~TestPage
    * /wconnect/TestPage.wwd


The CASE statement tries to find a matching 'process' signature and the calls the appropriate [Process class](vfps://Topic/_S9U01OFWB) to actually handle the request. The Process Prg file contains a small stub that loads the appropriate process class and executes its Process() method which does all the work of creating the output. The Process class manages output generation. All of this is handled by this single line of code in the Process CASE statement:

```foxpro
DO MyProcess.prg with THIS
```

### Server Configuration Object
For every server created a custom Configuration class is created. In the code above we have a `wcDemo` server which has a `wcDemoConfig` class that inherits from `wwServerConfig`. The Server Config has a set of default properties that get persisted into the `wcDemo.ini` file (where `wwDemo` is the name of the application) and are updated from the file when the server loads.

These custom config classes are added to the Server file so that you may extend the Config class with additional 'global' settings, simply by adding custom properties. These settings then get persisted to the INI file and are also read at startup. They are always accessible then as (from within a Process class):

```foxpro
Server.oConfig.cCustomProperty
```

#### Process Configuration Object
In addition each **Process class also creates a custom configuration object** that is specific to each Process class. In the example below there's a custom configuration for a `wwStore` Process with a host of custom properties. By convention these custom objects get attached to the server's oConfig object with the name of the process prefixed by an `o`.

So for a `wwStore` Process class it would look like this:

```foxpro
lcSqlConn = Server.oConfig.owwStore.cStoreSqlConnection
```

Here's what the config looks like for this scenario:

```foxpro
DEFINE CLASS wcDemoConfig as wwServerConfig

owwStore = .NULL.

FUNCTION Init
THIS.owwStore = CREATE("wwStoreConfig")
ENDFUNC

ENDDEFINE

DEFINE CLASS wwStoreConfig as RELATION

cHTMLPagePath = "d:\westwind\wwStore\"
cDATAPath = ".\wwStore\"
cXMLDocRoot = "wwstore"
cAdminUser = "Any"
cVirtualPath = "/wwstore/"
cStoreName = "West Wind Web Store"

*** This property is what's shown above
cStoreSqlConnection = "driver={Sql Server};server=(local);database=WebStore;"

ENDDEFINE
```

This configuration object is very powerful - simply add a property and any settings are persisted to the INI file and can then be changed in the INI file for configuration purposes.

Here's what the `wcDemo.ini` looks like with the `[main]` section and the `[wwStore]` section:


```ini
[Main]
Tempfilepath=.\temp\
Template=WC_
Timerinterval=200

Scriptmode=1

Debugmode=On
LiveReloadEnabled=On

# ... more settings


[Wwstore]
#... more settings

Storename=West Wind Technologies
StoreSqlconnection=driver={sql server};server=(local);database=WebStore;
```

> You can force the configuration file to write out settings via the **Status Form** and **Save Settings** button. This will write out **all values** into the `.ini` file even if they are empty or otherwise not set.