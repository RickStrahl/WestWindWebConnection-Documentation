Web Connection servers are designed to run as self-contained Visual FoxPro EXE files that can run independently of each other. Because each server interacts with the Web server through the Web Connection ISAPI extension each application requires a separate copy of wc.dll in a separate directory on the server.

The easiest way to create a new project is to use the Web Connection Management Console which takes you through the process. Make sure you pick the correct Web server and then proceed to specify a virtual directory for the application. Make sure you pick a separate directory, separate copy of wc.dll and a unique directory for your temp files:

![](IMAGES%5CMANAGEMENTCONSOLE%5CNEWPROJ2.GIF)

It's very important that you select the 'Copy separate copy of wc.dll into virtual' and pick a temp directory/template combination different from any existing Web Connection application to avoid having the two applications pick up message files from each other.

While the TEMP directory affects only file based operation, COM operation derives it's distinguising characteristics from different ProgId for the COM object created in your project. COM objects are generated with the name of hte project: Project.ProjectServer. COM applications thus distinguish themselves directly from each other through this class ID. Howver, a separate copy of wc.dll is still required in order to hold the instantiation information for these COM objects.

### If you're not using the Management Console
If you need to retrofit applications to run on a single server you can do so manually by editing the server and ISAPI INI files. The key items are:

**wc.ini:**  
PATH=c:\temp\wc\
TEMPLATE=WC_

**Your application INI file (project.ini):**  
tempfilepath=d:\temp\wc\
template=WC_

These paths and temp directories must match. Start and stop the Web Server (or you can update the changes for wc.ini from the Administration Web page).

### Running the same Server as separate Applications
In some cases you may need to run the same application on the same server. This means the same EXE file and same server names are used and a separate install is used to run against a different set of data.

There are two issues that make this process a bit involved:
<ul>
* Server Startup Path
* COM Operation
</ul>

In order to do this it's very important that you set the [wwServer::lUseRegistryForStartupPath](vfps://Topic/_1G50RGMA9) property to .F. Without this flag set Web Connection looks in the registry to find the server path and always starts out of the directory it finds there. If multiple applications are using this path they all point to the same directory which is not the desired result.

By setting the flag to .F. Web Connection will not look in the registry and instead always look for configuration in the EXE's startup directory. This allows Web Connection to run the same exact application multiple times on the server.

Prior to version 4.60 this could also be accomplished with the following code in YourAppServer::SetServerEnvironment:

```foxpro
THIS.cAppStartPath = GetAppStartPath()   && from wwUtils
SET DEFAULT TO (THIS.cAppStartPath)
THIS.cAppIniFile = THIS.cappstartpath + THIS.cAppName + ".ini"

...

THIS.oConfig = CREATE("wcDemoConfig")
THIS.oConfig.cFileName = THIS.cAppIniFile
```

This basically overrides the default that wwServer.GetAppStartPath() retrieves and connects to the new INI file and path. You can also use code like the above to use custom logic to find your server's startup path and INI file.

### COM Operation
If you're planning to do this with COM you have to do some additional work. The problem is that COM can only register a COM object in exactly one location pointing at exactly one EXE file. Hence you cannot register a COM object in more than one place.

**Separate EXE servers**  
This means that you either have to build another totally separate EXE for each application even if they are using the same code base or come up with a startup path scheme for a single COM server object. If you go that route make sure that you rename your server class so the COM ProgId changes.

If you need separate EXEs then you need to make sure to:
<ul>
* Create a **new** project preferably with a new EXE file name
* Change the server class name
</ul>
The above ensures that the ClassId and ProgIds are different for each server. Note that you can build your second project using the same source files as the first one with the exception maybe of the main Server class PRG. So although you have a different EXE you can still utilize the same exact codebase. 

The main drawback of this approach is administration of two distinct servers.

**Single EXE for shared apps**  
Another approach is to use a single COM server for multiple applications. Rather than build multiple EXEs you build a single EXE that routes to the proper directory. Inside of the EXE you then have multiple COM objects that have slightly different startup behavior but are otherwise the same.

The following creates three separate servers in MyApp.exe:

```foxpro
DEFINE CLASS MainServer as wwServer
... Main Web Connection Server implementation from your app
... This would be default code as usual
ENDDEFINE

* ** Implement a second class that inherits from the first
DEFINE CLASS MySecondaryServer as MainServer OLEPUBLIC

* ** Startup Path - retrieve from main INI file possibly
cStartPath = "d:\webapps\MySecondApp\"

FUNCTION SetServerEnvironment

* ** Base behavior
DoDefault()

* ** Set a new startup path - Server will change to this directory
THIS.cAppName = "My Second Server"
THIS.cAppStartPath = ADDBS(THIS.cStartPath)
	
ENDDEFINE

* ** Implement a third class that inherits from the first
DEFINE CLASS MyThirdServer as MainServer OLEPUBLIC

* ** Startup Path - retrieve from main INI file possibly
cStartPath = "d:\webapps\MyThirdApp\"

FUNCTION SetServerEnvironment

* ** Base behavior
DoDefault()

* ** Set a new startup path - Server will change to this directory
THIS.cAppName = "My Second Server"
THIS.cAppStartPath = ADDBS(THIS.cStartPath)
	
ENDDEFINE
```

Above you'd using some custom logic to figure out the StartupPath based on the class. The easiest and most configurable way likely would be to read configuration settings from the main server's INI file at startup to figure out the directories.

Using this approach you can maintain a single COM EXE and effectively run them out of separate directories. All you need to do is specify the correct server in the wc.ini [Automation Servers] section.

For the first app:
Server1=MyApp.MainServer
Server2=MyApp.MainServer

For the second app:
Server1=MyApp.MySecondServer
Server2=MyApp.MySecondServer

and so on. Each would live in their separate wc.ini file for the new application, but each would use the same EXE server.

Realize that this approach means that all servers are indeed identical and are based on the same codebase, so this works only if the codebase is maintained as a single base.