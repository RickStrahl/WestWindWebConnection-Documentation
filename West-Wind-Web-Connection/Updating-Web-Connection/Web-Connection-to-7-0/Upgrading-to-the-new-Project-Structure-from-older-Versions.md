Web Connection 6 and later introduces a new, folder based project system that makes it much easier to create self-contained 'projects' that are isolated from other projects, can be started up easily on the local machine and can be deployed easily on a server. There are build and support scripts that can automate much of the build and installation/configuration process.

v6 and later also introduces a new templating system with default templates that make it much easier to get started with nice looking, basic styling based on Bootstrap right out of the box. The templating and styling features are optional and if your old application doesn't use them it can continue to run using its own layouts and styles. You can choose whether to continue running your own styling, or use your own and add new functionality with the new features or whether to switch completely over to the new templates. The good news is you can keep running your old layouts alongside the new templates/styles so you can mix and match if you want.

## New Projects vs. Old
The key difference from old projects is that source code and Web Files are not stored in the Web Connection or other random folders, but rather are maintained **in a single well understood folder structure** that can be used to automate many operations like server configuration.

The new project system:

* Is completely isolated from other projects
* Uses a completely self contained project structure
* Uses folders and relative paths to find project files
* Is xcopy deployable
* Can quickly and easily be moved to a new location locally or on a server
* Can automate IIS configuration
* Can run without configuration for IIS Express and Web Connection Web Server
* Includes a self-contained local Web Server you can distribute for running internal or local apps

> #### @icon-warning Web Control Framework Applications
> If you have a Web Connection 5.x that uses the ASP.NET WebForms like **Web Control Framework**, please keep in mind that this technology has been discontinued in Web Connection 7.0. It is no longer supported in Web Connection 7.0 and disabled by default.
>
> However it can be enabled explicitly and **it still works and should continue to work with existing applications**. If you have such an application, it'll be best **not to update any UI, CSS, JS templates** and stick with the original UI libraries of your existing application.
>  
> If considering porting, the technology to look at is the [MVC framework](VFPS://Topic/_0I102WSAI) in Web Connection which provides many similar features using more traditional Model View Control architecture used by many other server frameworks.

## Upgrading from v6 to v7
These two versions are very similar in functionality so the differences between v6 and v7 are very, very minor. In most cases v6 applications 'just work' under Web Connection 7.0.

However, each update has version changes to templates, styles etc. and starting in v7 there's a helper that can update these resources for you using the [Project Update Wizard](https://webconnection.west-wind.com/docs/_2jw0bpb4d.htm#use-the-update-project-wizard-or-console-command) which will work with projects v6 forward. Note this can potentially break existing HTML layout due to changes in support libraries so you make sure **you backup before running the Wizard or manual steps**. After that check your app and see what UI issues you might run into. There's some additional CSS specific guidance in the [Bootstrap 3 to Bootstrap 4](VFPS://Topic/_5C9165COS) and [FontAwesome 4 to FontAwesome 5](VFPS://Topic/_5C916ZEB2) topics.

v7 added some additional features to the project system so it might be useful to 'upgrade' v6 projects by creating a new project and then copying the old files into the new project verbatim.

The differences are:  

* Styles and Library Updates (Bootstrap, FontAwesome)
* Template updates (slightly updated layouts for MVC templates)
* Generated project file updates

I'll cover these in the next section as they also apply to older version updates.

## Upgrading from pre-v6 Projects
If you are upgrading from a pre-V6 installation your best approach to creating a new project is: **Create a new project** and then copy the old projects files and code into the new project. This sounds harder than it is, but it makes sure you get all the latest project and deployment features and latest framework code updates running against your code. This is easier than the alternative of updating an existing project and structure with all the new files and updated code.

The idea here is by creating a new project you get all the latest features and support files required for the new project system as well as all the framework improvements in an already running application.

To do this:

* [Create a new Project with a new Name](VFPS://Topic/_S7R0OXD9T)
* Run the 'empty' project and make sure it works for test pages
* Copy over your Web Files into the `Web` folder
    * Static HTML files (HTML, CSS, JS libraries)
    * Web Connection HTML/FoxPro templates (scripts or templates)
    * Any other files that might be used by your Web site
* Copy your code into the new Server and Process classes
    * Copy any support classes (bus objects, utilities)
    * Copy any local data 
    * Cut and paste your Server and Process Classes into the new ones


### Copying existing Server Web Resources
If you have an older Web Connection application your server is likely using custom CSS that you created or some framework that Web Connection itself knows nothing about because it's specific to your app. You need to make sure these files are copied from the old Web location to the `Web` folder in the new project. This ensures that your existing HTML page references continue to work the same as they worked in the old project.

### Bootstrap and FontAwesome and the New Templates
Existing applications that don't use Bootstrap or FontAwesome **can continue to run just as before** pointing to your existing HTML and CSS resources. It still works the same but you won't see any of the enhancements for rendering and layout.

If you are doing a full bore upgrade you have to go through and update your existing HTML to use Bootstrap and FontAwesome and likely want to match the existing templates. Even if you don't use the Web Connection templates directly (ie. you're not using MVC or script/templates) you might want to take a look at those templates to see the base page layout that Web Connection uses. 

If you want to take advantage of some of the support library tools in the wwHtmlHelper classes, these have a dependency on Bootstrap to display correctly, but this is an optional feature. 

In fact most of the templating and rendering features are optional - none of these are requirements if you have an existing application that doesn't use them.

### Copying Web Connection Server and Process Code
The key part of the **new project upgrade** is getting a new project created that has all the new source files and scripts up to date. Most of this is merely new files that are updated and replace older files (if you're on a close version).

However, the two source code files involved - `yourAppServer` and `yourAppProcess` (where `yourApp` is the name of your application) - are two files that you should manually modify. The goal is to paste in your existing server configuration code and process class endpoint method code that comprises most of your application code.

### Server OnLoad and OnInit Updates
These two functions control configuration of Web Connection and are used to pre-initialize the server before it starts running requests. These reuqests set the servers initial load environment so it can find the configuration files (`OnInit()`) and the application configuration that configures application specific paths, environment settings, sets up globals used throughout, starts connnections etc. (`OnLoad()`).

In v7 the `OnLoad` handler has slightly changed behavior in that it fires **on the first request to the server**, rather than on startup as it used to. This ensures that the server `Init()` constructor is very light weight and won't crash in code that can't be debugged easily. Rather `OnLoad()` is now delayed and fires only when the first hit comes in.

Both `OnInit` and `OnLoad` code lives in `yourAppMain.prg` as part of the `YourAppServer` class. 

#### OnInit 
OnInit processing deals with setting up the configuration of the server and the default code that sets the config class typically doesn't change. Here's what the default `OnInit` processing looks like in a newly generated project:

```foxpro
PROTECTED FUNCTION OnInit

*** Location of the startup INI file 
THIS.cAppIniFile = addbs(THIS.cAppStartPath) + "wcDemo.ini"
* THIS.cAppName = "Web Connection Demo"

*** Override config with our own object which 
*** has sub-configs for applications.
*** wcDemoConfig is defined at the bottom of this PRG
THIS.oConfig = CREATEOBJECT("wcDemoConfig")
THIS.oConfig.cFileName = THIS.cAppIniFile

SET CENTURY ON

ENDFUNC


PROTECTED FUNCTION OnInitCompleted()

*** Customize the server's caption
IF THIS.lShowServerForm
	THIS.oServerForm.Caption = THIS.cServerId + " - " + ;
		                       THIS.cAppName + " "  +;
	                           WWVERSION
ENDIF
```

For most applications this code doesn't change, so in your old application check what your `OnInit` does and see if the code needs to be added to the new `OnInit`

#### OnLoad
The same goes for the `OnLoad` method except that `OnLoad` typically has application specific code that you added to this method. This means you likely have to move some of the code from the old `OnLoad` to the new method.

> In  Web Connection prior to v5, this method was called `SetServerProperties`

A default `OnLoad` looks like this:

```foxpro
PROTECTED FUNCTION OnLoad

*** This URL is executed when clicking on the Automation Server
*** Form's Exit button. It forces operation through a browser!
THIS.cCOMReleaseUrl=THIS.oConfig.cComReleaseUrl

*** Add persistent SQL Server Connection
#IF WWC_USE_SQL_SYSTEMFILES
    THIS.AddProperty("oSQL", CREATE("wwSQL"))
    IF !THIS.oSQL.Connect(THIS.oConfig.cSQLConnectString)
	    ERROR "Couldn't connect to SQL Service. Check your SQL Connect string in the application startup INI file." 
    ENDIF
#ENDIF

*** Add any of YOUR data paths and code 
*** SET DEFAULT is at EXE/Start path by default
SET PATH TO "..\Data" ADDITIVE   && optional

*** Force .NET version to 4.0
*DO wwDotnetBridge
*InitializeDotnetVersion("V4")  && Use Version 4

*** Add any SET CLASSLIB or SET PROCEDURE code here

ENDFUNC
* OnLoad
```

However, an application specific version is going to add additional libraries, set paths, make connections, configure globals etc. The following is an example of the Web Connection sample application:

```foxpro
PROTECTED FUNCTION OnLoad

*** This URL is executed when clicking on the Automation Server
*** Form's Exit button. It forces operation through a browser!
THIS.cCOMReleaseUrl=THIS.oConfig.cComReleaseUrl


*** Add support libraries that need to be explicitly loaded
SET PROCEDURE TO wwXMLState ADDITIVE
SET PROCEDURE TO wwRegEx ADDITIVE 
SET PROCEDURE TO wwSOAP ADDITIVE

SET PROCEDURE TO wwSmtp ADDITIVE
SET PROCEDURE TO wwPdf ADDITIVE
DO MarkdownParser

*** Add core libraries
SET PROCEDURE TO wwHtmlHelpers additive

*** Extended libraries that aren't auto-loaded
SET PROCEDURE TO wwRegEx ADDITIVE
SET PROCEDURE TO wwXMLState ADDITIVE

*** Remeber these may not be relative in COM object
*** hence the full path!
DO PATH WITH THIS.cAppStartpath && Required when running as InProc COM object
DO PATH WITH THIS.cAppStartPath + "CLASSES\"
DO PATH WITH THIS.cAppStartPath + "TOOLS\"

DO PATH WITH THIS.cAppStartPath +"Samples\WWDEMO\"
DO PATH WITH THIS.cAppStartPath + "Samples\MusicStore\"

DO PATH WITH THIS.cAppStartPath + "WW\"

*** WebLog Web Controls Demo
DO PATH WITH this.cAppStartPath + "Samples\WebLog\"
DO PATH WITH this.cAppStartPath + "Samples\WebLog\data\"

*** Sample libraries
SET PROCEDURE TO WebLog_Routines ADDITIVE
SET PROCEDURE TO WebLog_Business ADDITIVE
SET PROCEDURE TO plUploadHandler Additive


*** Load Music Store dependencies
DO MusicStore

*** Environment Variables

*** Avoid problems in the demo
SET STRICTDATE TO 0
SET REPROCESS TO 1 SECOND
SET NULLDISPLAY TO "N/A"

*** Control VFP Memory background Settings
*SYS(3050,2,THIS.oConfig.nMemUsage)

*** Load up any SQL Connections
#IF WWC_USE_SQL_SYSTEMFILES
	THIS.oSQL = CREATE("wwSQL")
	IF !THIS.oSQL.Connect(THIS.oConfig.cSQLConnectString)
	   MESSAGEBOX("Couldn't connect to system SQL Service. Check your SQL Connect string",48,"Web Connection")
	   CANCEL
    ENDIF
    this.oSql.EnableUnicodeToAnsiMapping(.T.)
#ENDIF	


*** Sql Connection to News Feed
ADDPROPERTY(this,"oSqlNews",CREATEOBJECT("wwSql"))
this.oSqlNews.Connect("server=.;database=user;integrated security=true")

ENDFUNC
* OnLoad
```

The content of this method obviously will be very application specific, and if you are bringing code over from a previous version, you'll be copying in the code from that old version.

The key is if you reference code here, to make sure you `SET PROCEDURE TO` and or `SET CLASSLIB TO` the appropriate classes so any code you reference can run properly.

### Process Class - Copy EndPoint Methods
The process class is really the meat of most applications, and this is where you really want to reuse all of your code. The good news is that for the most part Process code hasn't changed very much between versions, unless you used the Web Control Framework which did things very differently using self contained page files. Standard process methods typically either use scripts or templates, or alternately generate HTML directly. 

These methods can generally just be copied to the new class that was generated by the new project wizard.

To do this you want to:

* Copy your Endpoint methods from the old Class
* Paste them into the new class
* Consolidate `OnProcessInit()`, `StandardPage`, `ErrorPage` and any other `OnXXX` methods

A generated wwProcess class looks like this:

```foxpro
*************************************************************
DEFINE CLASS TestProcess AS WWC_PROCESS
*************************************************************

*** Response class used - override as needed
cResponseClass = [WWC_PAGERESPONSE]

*** Default for page script processing if no method exists
*** 1 - MVC Template (ExpandTemplate()) 
*** 2 - Web Control Framework Pages
*** 3 - MVC Script (ExpandScript())
nPageScriptMode = 3

*!* cAuthenticationMode = "UserSecurity"  && `Basic` is default


*** ADD PROCESS CLASS EXTENSIONS ABOVE - DO NOT MOVE THIS LINE ***


#IF .F.
* Intellisense for THIS
LOCAL THIS as TestProcess OF TestProcess.prg
#ENDIF
 
*********************************************************************
* Function TestProcess :: OnProcessInit
************************************
*** If you need to hook up generic functionality that occurs on
*** every hit against this process class , implement this method.
*********************************************************************
FUNCTION OnProcessInit

*!* LOCAL lcScriptName, llForceLogin
*!*	THIS.InitSession("MyApp")
*!*
*!*	lcScriptName = LOWER(JUSTFNAME(Request.GetPhysicalPath()))
*!*	llIgnoreLoginRequest = INLIST(lcScriptName,"default","login","logout")
*!*
*!*	IF !THIS.Authenticate("any","",llIgnoreLoginRequest) 
*!*	   IF !llIgnoreLoginRequest
*!*		  RETURN .F.
*!*	   ENDIF
*!*	ENDIF

*** Explicitly specify that pages should encode to UTF-8 
*** Assume all form and query request data is UTF-8
Response.Encoding = "UTF8"
Request.lUtf8Encoding = .T.


*** Add CORS header to allow cross-site access from other domains/mobile devices on Ajax calls
*!* Response.AppendHeader("Access-Control-Allow-Origin","*")
*!* Response.AppendHeader("Access-Control-Allow-Origin",Request.ServerVariables("HTTP_ORIGIN"))
*!* Response.AppendHeader("Access-Control-Allow-Methods","POST, GET, DELETE, PUT, OPTIONS")
*!* Response.AppendHeader("Access-Control-Allow-Headers","Content-Type, *")
*!* *** Allow cookies and auth headers
*!* Response.AppendHeader("Access-Control-Allow-Credentials","true")
*!* 
*!* *** CORS headers are requested with OPTION by XHR clients. OPTIONS returns no content
*!*	lcVerb = Request.GetHttpVerb()
*!*	IF (lcVerb == "OPTIONS")
*!*	   *** Just exit with CORS headers set
*!*	   *** Required to make CORS work from Mobile devices
*!*	   RETURN .F.
*!*	ENDIF   


RETURN .T.
ENDFUNC




*********************************************************************
FUNCTION TestPage()
************************

THIS.StandardPage("Hello World from the TestProcess process",;
                  "If you got here, everything is working fine.<p>" + ;
                  "Server Time: <b>" + TIME()+ "</b>")
                  
ENDFUNC
* EOF TestPage

...

ENDCLASS
```


* Copy all operational methods and replace `TestPage()`

From your old class copy the actual endpoint methods that handle a request - as well as any helper methods - and then replace `TestPage()` with that code in the new class.

* Consolidate Properties

If your process class has custom properties copy them from the old class and paste them into the new class.

* Consolidate `OnProcessInit`

Then look at `OnProcessInit()` in your old class and copy the method body code and paste it into the new class' `OnProcessInit()` below the commented code.

### Old versions: VCX to PRG Classes
If you have a pre v7 version you'll also need to update your class references from various VCX classes to PRG classes.

* [VCX Class to PRG Migrations](https://webconnection.west-wind.com/docs/_5c915obhg.htm)

If you have very old (v4 and older) versions of Web Connection, many of the support classes where contained in the `wwIPStuff.vcx` class. That class has been removed and replaced with individual PRG classes. Classes like `wwSmtp`, `wwFtp`, etc. replace the classes that previously lived in `wwIpStuff.vcx`. This requires explicitly `SET PROCEDURE TO wwSmtp ADDITVE` for each class used instead of the single `SET CLASSLIB TO wwIPStuff ADDITIVE`. Some of the functions that used to be on the `wwIPStuff` class have also been moved into their own respective classes, such as the `wwSocket` class and `wwHttp` classes. The best strategy for these is to search for the specific old class names and make sure they are instantiated correctly and/or rename them.